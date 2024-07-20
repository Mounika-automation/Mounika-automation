import io.restassured.RestAssured;
import io.restassured.response.Response;
import org.junit.Test;

public class WeatherStatisticsTest {

    @Test
    public void testGetTopNCitiesWithLowestTemperature() {
        // Read city.csv file and extract city names
        List<String> cities = readCityCSVFile();

        // Use Openweathermap Geocoding API to get latitudes and longitudes for each city
        List<GeoCodingResponse> geoCodingResponses = new ArrayList<>();
        for (String city : cities) {
            Response response = RestAssured.given()
                    .baseUri("https://api.openweathermap.org/geo/1.0/")
                    .queryParam("q", city)
                    .queryParam("appid", "YOUR_API_KEY")
                    .get();
            GeoCodingResponse geoCodingResponse = response.getBody().as(GeoCodingResponse.class);
            geoCodingResponses.add(geoCodingResponse);
        }

        // Use Openweathermap One Call API to get weather report for each city
        List<WeatherResponse> weatherResponses = new ArrayList<>();
        for (GeoCodingResponse geoCodingResponse : geoCodingResponses) {
            Response response = RestAssured.given()
                    .baseUri("https://api.openweathermap.org/data/3.0/onecall/")
                    .queryParam("lat", geoCodingResponse.getLat())
                    .queryParam("lon", geoCodingResponse.getLon())
                    .queryParam("appid", "YOUR_API_KEY")
                    .get();
            WeatherResponse weatherResponse = response.getBody().as(WeatherResponse.class);
            weatherResponses.add(weatherResponse);
        }

        // Extract temperature from weather report
        List<Double> temperatures = new ArrayList<>();
        for (WeatherResponse weatherResponse : weatherResponses) {
            temperatures.add(weatherResponse.getTemp());
        }

        // Sort cities by temperature in ascending order
        Collections.sort(temperatures);

        // Return top N cities with the lowest temperature
        List<String> topNCities = new ArrayList<>();
        for (int i = 0; i < 3; i++) {
            topNCities.add(cities.get(i));
        }
        System.out.println("Top 3 cities with the lowest temperature: " + topNCities);
    }

    @Test
    public void testGetTopNCitiesWithHighestHumidity() {
        // Read city.csv file and extract city names
        List<String> cities = readCityCSVFile();

        // Use Openweathermap Geocoding API to get latitudes and longitudes for each city
        List<GeoCodingResponse> geoCodingResponses = new ArrayList<>();
        for (String city : cities) {
            Response response = RestAssured.given()
                    .baseUri("https://api.openweathermap.org/geo/1.0/")
                    .queryParam("q", city)
                    .queryParam("appid", "YOUR_API_KEY")
                    .get();
            GeoCodingResponse geoCodingResponse = response.getBody().as(GeoCodingResponse.class);
            geoCodingResponses.add(geoCodingResponse);
        }

              // Use Openweathermap One Call API to get weather report for each city
        List<WeatherResponse> weatherResponses = new ArrayList<>();
        for (GeoCodingResponse geoCodingResponse : geoCodingResponses) {
            Response response = RestAssured.given()
                    .baseUri("https://api.openweathermap.org/data/3.0/onecall/")
                    .queryParam("lat", geoCodingResponse.getLat())
                    .queryParam("lon", geoCodingResponse.getLon())
                    .queryParam("appid", "YOUR_API_KEY")
                    .get();
            WeatherResponse weatherResponse = response.getBody().as(WeatherResponse.class);
            weatherResponses.add(weatherResponse);
        }

        // Extract humidity from weather report
        List<Double> humidities = new ArrayList<>();
        for (WeatherResponse weatherResponse : weatherResponses) {
            humidities.add(weatherResponse.getHumidity());
        }

        // Sort cities by humidity in descending order
        Collections.sort(humidities, Collections.reverseOrder());

        // Return top N cities with the highest humidity
        List<String> topNCities = new ArrayList<>();
        for (int i = 0; i < 3; i++) {
            topNCities.add(cities.get(i));
        }
        System.out.println("Top 3 cities with the highest humidity: " + topNCities);
    }

    private List<String> readCityCSVFile() {
        // Read city.csv file and extract city names
        List<String> cities = new ArrayList<>();
        try (BufferedReader br = new BufferedReader(new FileReader("city.csv"))) {
            String line;
            while ((line = br.readLine()) != null) {
                cities.add(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return cities;
    }
}

class GeoCodingResponse {
    private double lat;
    private double lon;

    public double getLat() {
        return lat;
    }

    public double getLon() {
        return lon;
    }
}

class WeatherResponse {
    private double temp;
    private double humidity;

    public double getTemp() {
        return temp;
    }

    public double getHumidity() {
        return humidity;
    }
}
