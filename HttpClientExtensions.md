```
using System;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Text.Json;
using System.Threading;
using System.Threading.Tasks;

public static class HttpClientExtensions
{
    public static async Task<HttpResponseMessage> SendAsync(
        this HttpClient httpClient,
        HttpMethod method,
        string url,
        object? data = null,
        Dictionary<string, string>? headers = null,
        string? authType = null,
        string? authToken = null,
        CancellationToken cancellationToken = default)
    {
        using var request = new HttpRequestMessage(method, url);

        // Add JSON body if present
        if (data != null && method != HttpMethod.Get && method != HttpMethod.Head)
        {
            string json = JsonSerializer.Serialize(data);
            request.Content = new StringContent(json, Encoding.UTF8, "application/json");
        }

        // Add headers
        if (headers != null)
        {
            foreach (var kv in headers)
                request.Headers.TryAddWithoutValidation(kv.Key, kv.Value);
        }

        // Add authorization
        if (!string.IsNullOrWhiteSpace(authType) && !string.IsNullOrWhiteSpace(authToken))
            request.Headers.Authorization = new AuthenticationHeaderValue(authType, authToken);

        // Send the request
        return await httpClient.SendAsync(request, HttpCompletionOption.ResponseHeadersRead, cancellationToken);
    }

    /// <summary>
    /// Sends a request and returns a structured ApiResponse with deserialized data.
    /// </summary>
    public static async Task<ApiResponse<T>> SendAsync<T>(
        this HttpClient httpClient,
        HttpMethod method,
        string url,
        object? data = null,
        Dictionary<string, string>? headers = null,
        string? authType = null,
        string? authToken = null,
        CancellationToken cancellationToken = default)
    {
        try
        {
            using var response = await httpClient.SendAsync(method, url, data, headers, authType, authToken, cancellationToken);
            var content = await response.Content.ReadAsStringAsync(cancellationToken);

            var apiResponse = new ApiResponse<T>
            {
                StatusCode = response.StatusCode,
                RawContent = content
            };

            // ✅ Handle successful HTTP responses
            if (response.IsSuccessStatusCode)
            {
                if (string.IsNullOrWhiteSpace(content))
                {
                    apiResponse.Success = true;
                    apiResponse.Message = "Success (empty response).";
                    return apiResponse;
                }

                if (TryDeserialize(content, out T? result))
                {
                    apiResponse.Data = result;
                    apiResponse.Success = true; // ✅ Set success properly here
                    apiResponse.Message = "Success";
                }
                else
                {
                    apiResponse.Success = false;
                    apiResponse.Message = "Response could not be deserialized into the expected type.";
                }
            }
            else
            {
                // Non-success HTTP code
                apiResponse.Success = false;
                apiResponse.Message = $"Request failed with status {(int)response.StatusCode}: {response.ReasonPhrase}";
            }

            return apiResponse;
        }
        catch (TaskCanceledException)
        {
            return new ApiResponse<T>
            {
                Success = false,
                Message = "Request timed out or was cancelled.",
                StatusCode = HttpStatusCode.RequestTimeout
            };
        }
        catch (HttpRequestException ex)
        {
            return new ApiResponse<T>
            {
                Success = false,
                Message = $"HTTP request error: {ex.Message}",
                StatusCode = HttpStatusCode.BadRequest
            };
        }
        catch (Exception ex)
        {
            return new ApiResponse<T>
            {
                Success = false,
                Message = $"Unexpected error: {ex.Message}",
                StatusCode = HttpStatusCode.InternalServerError
            };
        }
    }

    /// <summary>
    /// Safely deserializes JSON without throwing exceptions.
    /// </summary>
    public static bool TryDeserialize<T>(string json, out T? result)
    {
        try
        {
            result = JsonSerializer.Deserialize<T>(json, new JsonSerializerOptions
            {
                PropertyNameCaseInsensitive = true
            });

            return result != null;
        }
        catch (JsonException ex)
        {
            Console.WriteLine($"{typeof(T).Name} JSON deserialization failed: {ex.Message}");
            result = default;
            return false;
        }
    }
}

/// <summary>
/// Represents a consistent, safe API response structure for HTTP requests.
/// </summary>
public class ApiResponse<T>
{
    public bool Success { get; set; }
    public HttpStatusCode StatusCode { get; set; }
    public string? Message { get; set; }
    public T? Data { get; set; }
    public string? RawContent { get; set; }
}

```
