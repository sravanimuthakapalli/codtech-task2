//fteching weather data

import java.io.IOException;

import java.net.URI;

import java.net.http.HttpClient

import java.net.http.HttpRequest;

import java.net.http.HttpResponse;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;

public class WeatherApp {

    private static final String API_KEY = "YOUR_API_KEY"; // Replace with your API key
    private static final String BASE_URL = "https://api.openweathermap.org/data/2.5/weather";

    public static void main(String[] args) {
        String city = "London"; // Example city, replace with user input or other logic

        try {
            String weatherData = getWeatherData(city);
            displayWeatherData(weatherData);
        } catch (IOException | InterruptedException e) {
            System.err.println("Error: " + e.getMessage());
            e.printStackTrace();
        }
    }

    public static String getWeatherData(String city) throws IOException, InterruptedException {
        String apiUrl = String.format("%s?q=%s&appid=%s&units=metric", BASE_URL, city, API_KEY);

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(apiUrl))
                .build();

        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

        if (response.statusCode() == 200) {
            return response.body();
        } else {
            throw new IOException("API request failed with status code: " + response.statusCode());
        }
    }

    public static void displayWeatherData(String weatherData) throws IOException {
        ObjectMapper objectMapper = new ObjectMapper();
        JsonNode root = objectMapper.readTree(weatherData);

        String cityName = root.get("name").asText();
        double temperature = root.get("main").get("temp").asDouble();
        String description = root.get("weather").get(0).get("description").asText();
        double windSpeed = root.get("wind").get("speed").asDouble();


        System.out.println("Weather in " + cityName + ":");
        System.out.println("  Temperature: " + temperature + " °C");
        System.out.println("  Description: " + description);
        System.out.println("  Wind Speed: " + windSpeed + " m/s");

    }
}
