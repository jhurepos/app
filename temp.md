Here’s a JavaScript script that calculates personalized 95% confidence intervals given a beta coefficient vector and a variance-covariance matrix, both imported as `.csv` files. You can use the `papaparse` library to parse CSV files.

### Prerequisites
- Include `papaparse` for CSV parsing. You can add it via a CDN or npm.

```html
<script src="https://cdn.jsdelivr.net/npm/papaparse@5.3.1/papaparse.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjs/11.7.0/math.min.js"></script>
```

### JavaScript Code
```javascript
document.addEventListener("DOMContentLoaded", () => {
  // File input elements
  const betaInput = document.getElementById("beta-file");
  const covInput = document.getElementById("cov-file");
  const resultDiv = document.getElementById("result");

  // Event listener for file uploads
  document.getElementById("upload-btn").addEventListener("click", async () => {
    try {
      // Parse CSV files
      const betaCsv = await parseCsv(betaInput.files[0]);
      const covCsv = await parseCsv(covInput.files[0]);

      // Convert parsed data into usable arrays
      const betaVector = betaCsv.map(row => parseFloat(row[0]));
      const covMatrix = covCsv.map(row => row.map(Number));

      // Validate input dimensions
      if (betaVector.length !== covMatrix.length || !covMatrix.every(row => row.length === covMatrix.length)) {
        throw new Error("Dimensions of beta vector and covariance matrix do not match.");
      }

      // Calculate confidence intervals
      const confidenceIntervals = calculateConfidenceIntervals(betaVector, covMatrix, 0.95);

      // Display results
      resultDiv.innerHTML = confidenceIntervals
        .map((ci, idx) => `Parameter ${idx + 1}: [${ci.lower.toFixed(4)}, ${ci.upper.toFixed(4)}]`)
        .join("<br>");
    } catch (error) {
      resultDiv.textContent = `Error: ${error.message}`;
    }
  });

  // Function to parse CSV files
  function parseCsv(file) {
    return new Promise((resolve, reject) => {
      Papa.parse(file, {
        complete: result => resolve(result.data),
        error: err => reject(err)
      });
    });
  }

  // Function to calculate 95% confidence intervals
  function calculateConfidenceIntervals(beta, covMatrix, confidenceLevel) {
    const zScore = 1.96; // Approx for 95%
    return beta.map((b, i) => {
      const standardError = Math.sqrt(covMatrix[i][i]);
      return {
        lower: b - zScore * standardError,
        upper: b + zScore * standardError
      };
    });
  }
});
```

### HTML Interface
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Confidence Interval Calculator</title>
</head>
<body>
  <h1>95% Confidence Interval Calculator</h1>
  <label for="beta-file">Upload Beta Coefficient CSV:</label>
  <input type="file" id="beta-file" accept=".csv"><br><br>
  <label for="cov-file">Upload Covariance Matrix CSV:</label>
  <input type="file" id="cov-file" accept=".csv"><br><br>
  <button id="upload-btn">Calculate Confidence Intervals</button>
  <h2>Results:</h2>
  <div id="result"></div>
</body>
</html>
```

### Instructions
1. Save the script and HTML code to a file (e.g., `index.html`).
2. Open the file in a web browser.
3. Upload the beta coefficients and covariance matrix as CSV files.
4. Click "Calculate Confidence Intervals" to see the results.

### Expected CSV Formats
- **Beta Coefficient CSV**: A single column of beta coefficients.
  ```csv
  1.5
  -0.3
  0.8
  ```
- **Covariance Matrix CSV**: A square matrix of variances and covariances.
  ```csv
  0.04,0.01,0.02
  0.01,0.09,0.03
  0.02,0.03,0.16
  ```

Let me know if you need additional customization or features!
