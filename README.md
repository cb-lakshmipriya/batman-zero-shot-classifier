# batman-zero-shot-classifier
### Instructions to Run the Script

1. **Install Dependencies**:
   Ensure you’re in the project directory (`/Users/cb-it-01-1737/poc/zero-shot-classifier/`) and install the required packages:
   ```bash
   npm install papaparse @xenova/transformers
   ```

2. **Prepare CSV Files**:
   - Ensure `reasons.csv` and `zero-shot-analysis - candidate_labels.csv` are in the same directory as `batman_classifier.js` (`/Users/cb-it-01-1737/poc/zero-shot-classifier/`).
   - Verify that the CSV files match the structure provided in your earlier message (e.g., `reasons.csv` has columns like `id`, `reason`, etc., and `zero-shot-analysis - candidate_labels.csv` has columns `reason`, `action_type`, `job_type`, `gateway`).

3. **Set Hugging Face Token (if needed)**:
   If the `Xenova/mobilebert-uncased-mnli` model requires authentication, set your Hugging Face API token as an environment variable:
   ```bash
   export HF_TOKEN='your_hugging_face_token'
   ```
   Replace `'your_hugging_face_token'` with your actual token. Alternatively, update the `env.hfAccessToken` line in the script with your token directly.

4. **Run the Script**:
   Execute the script using Node.js:
   ```bash
   node batman_classifier.js
   ```

5. **Check Output**:
   - The script will process each row of `reasons.csv`, classify the `reason` column for all four categories, and save the results to `classified_reasons_output.csv` in the same directory.
   - It will also log progress and a sample of the output to the console.

### Expected Output

- **Output File**: A file named `classified_reasons_output.csv` will be created with the original columns from `reasons.csv` plus four new columns: `classified_reason`, `classified_action_type`, `classified_job_type`, and `classified_gateway`.
- **Sample Row** (based on the first row of `reasons.csv`):
  ```
  id,reason,time,isRepeat,isChecked,gw_type,job_type,action_type,domain,classified_reason,classified_action_type,classified_job_type,classified_gateway
  1,stripe payment collection failed. reason: (card_declined) your card has insufficient funds.,2024-12-02 19:16:41,0,1,stripe,,/api/v2/invoices/([^/]+)/collect_payment/?,calendly-billing,Insufficient Funds,collect_invoice,collect_invoice,stripe
  ```
  (Actual classifications depend on the model’s худ4

- **Console Output**: The script logs progress, including model loading, CSV parsing, classification for each row, and a preview of the output CSV.

### Troubleshooting Tips

- **If `papaparse` Error Persists**:
  - Verify that `npm install papaparse` ran successfully. Check `node_modules/papaparse` exists.
  - If not, try clearing the npm cache and reinstalling:
    ```bash
    npm cache clean --force
    npm install papaparse
    ```

- **If Model Loading Fails**:
  - Ensure a stable internet connection, as the script downloads the `Xenova/mobilebert-uncased-mnli` model from Hugging Face.
  - Check that your Hugging Face API token is valid if authentication is required.
  - Try an alternative model, e.g., replace `'Xenova/mobilebert-uncased-mnli'` with `'Xenova/distilbert-base-uncased-finetuned-sst-2-english'` in the `loadClassifier` call.

- **If CSV Files Are Not Found**:
  - Confirm that `reasons.csv` and `zero-shot-analysis - candidate_labels.csv` are in `/Users/cb-it-01-1737/poc/zero-shot-classifier/`.
  - Check file permissions using:
    ```bash
    ls -l reasons.csv "zero-shot-analysis - candidate_labels.csv"
    ```
    Ensure they are readable (`-r--` or better).

- **If Classification Results Are Inaccurate**:
  - The `mobilebert-uncased-mnli` model may struggle with short or ambiguous reasons (e.g., “restricted”, “refused”). Consider refining the candidate labels in `zero-shot-analysis - candidate_labels.csv` to be more specific.
  - Test with a smaller subset of `reasons.csv` (e.g., first 10 rows) by modifying the loop:
    ```javascript
    for (const row of reasonsData.slice(0, 10)) {
    ```

- **Performance Concerns**:
  - Processing 4550 rows with four classifications each can be slow due to sequential API calls. For faster execution, you could explore parallelizing classifications, but this requires advanced handling with `@xenova/transformers`.
  - Monitor memory usage, as Node.js v23.11.0 may consume significant resources for large datasets. If memory issues arise, consider processing in chunks:
    ```javascript
    const chunkSize = 100;
    for (let i = 0; i < reasonsData.length; i += chunkSize) {
      const chunk = reasonsData.slice(i, i + chunkSize);
      for (const row of chunk) {
        // Classification logic
      }
    }
    ```

### Additional Notes

- **Current Date and Time**: Your message indicates it’s 11:48 PM IST on Tuesday, May 13, 2025. This aligns with the latest timestamp in `reasons.csv` (2025-05-13), suggesting the data is current as of your request.
- **CSV File Locations**: The script assumes `reasons.csv` and `zero-shot-analysis - candidate_labels.csv` are in the project directory. If they’re elsewhere, update the `reasonsPath` and `labelsPath` variables with the correct paths, e.g.:
  ```javascript
  const reasonsPath = '/path/to/reasons.csv';
  const labelsPath = '/path/to/zero-shot-analysis - candidate_labels.csv';
  ```
- **Output Customization**: If you prefer a different output format (e.g., JSON) or need specific columns, let me know, and I can adjust the `Papa.unparse` call or add a JSON export:
  ```javascript
  await fs.writeFile(path.join(__dirname, 'classified_reasons_output.json'), JSON.stringify(results, null, 2), 'utf-8');
  ```

If you encounter further errors or need assistance with specific rows, model accuracy, or output formatting, please provide details (e.g., error messages, sample output, or specific row IDs), and I’ll help refine the solution. Let me know once you’ve run the script, and I can review the output or address any issues!
