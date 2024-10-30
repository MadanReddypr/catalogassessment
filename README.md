# catalogassessment

<code>

import org.json.JSONObject;
import java.util.ArrayList;
import java.util.List;
import java.util.*;
import java.math.BigInteger;

public class ShamirSecretSharing {

    // Convert a value from a specified base to decimal using BigInteger
    public static BigInteger baseToDecimal(String value, int base) {
        return new BigInteger(value, base);
    }

    // Perform Lagrange interpolation to find f(x) given points (xVals, yVals) using BigInteger
    public static BigInteger lagrangeInterpolation(List<BigInteger> xVals, List<BigInteger> yVals, BigInteger x) {
        BigInteger total = BigInteger.ZERO;
        int k = xVals.size();

        for (int i = 0; i < k; i++) {
            BigInteger term = yVals.get(i);
            for (int j = 0; j < k; j++) {
                if (i != j) {
                    BigInteger numerator = x.subtract(xVals.get(j));
                    BigInteger denominator = xVals.get(i).subtract(xVals.get(j));
                    term = term.multiply(numerator).divide(denominator);
                }
            }
            total = total.add(term);
        }
        
        return total;
    }

    // Main function to find the constant term (c) of the polynomial
    public static BigInteger findConstantTerm(String jsonString) {
        // Parse JSON input
        JSONObject data = new JSONObject(jsonString);
        JSONObject keys = data.getJSONObject("keys");

        int n = keys.getInt("n");
        int k = keys.getInt("k");

        // Prepare x and y values in decimal
        List<BigInteger> xVals = new ArrayList<>();
        List<BigInteger> yVals = new ArrayList<>();

        // Extract points and convert to decimal
        for (String key : data.keySet()) {
            if (!key.equals("keys")) {
                JSONObject point = data.getJSONObject(key);
                int base = point.getInt("base");
                String value = point.getString("value");

                BigInteger xDecimal = new BigInteger(key); // x-coordinate from JSON key
                BigInteger yDecimal = baseToDecimal(value, base);

                xVals.add(xDecimal);
                yVals.add(yDecimal);
            }
        }

        // Check if we have enough points
        if (xVals.size() < k) {
            throw new IllegalArgumentException("Not enough points to solve the polynomial.");
        }

        // Find the constant term (f(0)) using Lagrange interpolation
        return lagrangeInterpolation(xVals, yVals, BigInteger.ZERO);
    }

    public static void main(String[] args) {
        // Sample input JSON as provided in the problem
        String jsonString = """
        {
            "keys": {
                "n": 10,
                "k": 7
            },
            "1": {
                "base": "6",
                "value": "13444211440455345511"
            },
            "2": {
                "base": "15",
                "value": "aed7015a346d63"
            },
            "3": {
                "base": "15",
                "value": "6aeeb69631c227c"
            },
            "4": {
                "base": "16",
                "value": "e1b5e05623d881f"
            },
            "5": {
                "base": "8",
                "value": "316034514573652620673"
            },
            "6": {
                "base": "3",
                "value": "2122212201122002221120200210011020220200"
            },
            "7": {
                "base": "3",
                "value": "20120221122211000100210021102001201112121"
            },
            "8": {
                "base": "6",
                "value": "20220554335330240002224253"
            },
            "9": {
                "base": "12",
                "value": "45153788322a1255483"
            },
            "10": {
                "base": "7",
                "value": "1101613130313526312514143"
            }
        }
        """;

        // Calculate and print the constant term
        BigInteger constantTerm = findConstantTerm(jsonString);
        System.out.println("Constant term (c) of the polynomial: " + constantTerm);
    }
}
