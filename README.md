# catalog
import org.json.JSONArray;
import org.json.JSONObject;
import java.io.FileReader;
import java.io.IOException;
import java.math.BigInteger;
public class Main{
    public static void main(String[] args) {
        try {
            String jsonFilePath = "testcase.json";
            JSONObject testCase = new JSONObject(readFile(jsonFilePath));
            int k = testCase.getInt("k");
            JSONArray keys = testCase.getJSONArray("keys");

            int n = keys.length();
            BigInteger[] x = new BigInteger[n];
            BigInteger[] y = new BigInteger[n];

            for (int i = 0; i < n; i++) {
                JSONObject root = keys.getJSONObject(i);
                String base = root.getString("base");
                String value = root.getString("value");
                x[i] = BigInteger.valueOf(i + 1);
                y[i] = new BigInteger(value, Integer.parseInt(base));
            }

            BigInteger constantTerm = findConstantTerm(x, y, k);
            System.out.println("Secret (Constant Term): " + constantTerm);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static String readFile(String filePath) throws IOException {
        FileReader reader = new FileReader(filePath);
        StringBuilder content = new StringBuilder();
        int ch;
        while ((ch = reader.read()) != -1) {
            content.append((char) ch);
        }
        reader.close();
        return content.toString();
    }

    public static BigInteger findConstantTerm(BigInteger[] x, BigInteger[] y, int k) {
        BigInteger constant = BigInteger.ZERO;

        for (int i = 0; i < k; i++) {
            BigInteger numerator = BigInteger.ONE;
            BigInteger denominator = BigInteger.ONE;

            for (int j = 0; j < k; j++) {
                if (i != j) {
                    numerator = numerator.multiply(x[j].negate());
                    denominator = denominator.multiply(x[i].subtract(x[j]));
                }
            }

            BigInteger term = y[i].multiply(numerator).divide(denominator);
            constant = constant.add(term);
        }

        return constant;
    }
}
