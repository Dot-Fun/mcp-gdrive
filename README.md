# Google Drive server

This MCP server integrates with Google Drive to allow listing, reading, and searching files, as well as the ability to read and write to Google Sheets.

This project includes code originally developed by Anthropic, PBC, licensed under the MIT License from [this repo](https://github.com/modelcontextprotocol/servers/tree/main/src/gdrive).

## Components

### Tools

- **gdrive_search**
  - **Description**: Search for files in Google Drive.
  - **Input**:
    - `query` (string): Search query.
    - `pageToken` (string, optional): Token for the next page of results.
    - `pageSize` (number, optional): Number of results per page (max 100).
  - **Output**: Returns file names and MIME types of matching files.

- **gdrive_read_file**
  - **Description**: Read contents of a file from Google Drive.
  - **Input**:
    - `fileId` (string): ID of the file to read.
  - **Output**: Returns the contents of the specified file.

- **gdrive_deauthenticate**
  - **Description**: Remove Google Drive authentication credentials and deauthenticate the server.
  - **Input**: None required.
  - **Output**: Confirmation message. Note: After using this tool, you must restart your MCP client to re-authenticate.

- **gsheets_read**
  - **Description**: Read data from a Google Spreadsheet with flexible options for ranges and formatting.
  - **Input**:
    - `spreadsheetId` (string): The ID of the spreadsheet to read.
    - `ranges` (array of strings, optional): Optional array of A1 notation ranges (e.g., `['Sheet1!A1:B10']`). If not provided, reads the entire sheet.
    - `sheetId` (number, optional): Specific sheet ID to read. If not provided with ranges, reads the first sheet.
  - **Output**: Returns the specified data from the spreadsheet.

- **gsheets_update_cell**
  - **Description**: Update a cell value in a Google Spreadsheet.
  - **Input**:
    - `fileId` (string): ID of the spreadsheet.
    - `range` (string): Cell range in A1 notation (e.g., `'Sheet1!A1'`).
    - `value` (string): New cell value.
  - **Output**: Confirms the updated value in the specified cell.

### Resources

The server provides access to Google Drive files:

- **Files** (`gdrive:///<file_id>`)
  - Supports all file types
  - Google Workspace files are automatically exported:
    - Docs → Markdown
    - Sheets → CSV
    - Presentations → Plain text
    - Drawings → PNG
  - Other files are provided in their native format

## Getting started

1. [Create a new Google Cloud project](https://console.cloud.google.com/projectcreate)
2. [Enable the Google Drive API](https://console.cloud.google.com/workspace-api/products)
3. [Configure an OAuth consent screen](https://console.cloud.google.com/apis/credentials/consent) ("internal" is fine for testing)
4. Add OAuth scopes `https://www.googleapis.com/auth/drive.readonly`, `https://www.googleapis.com/auth/spreadsheets`
5. In order to allow interaction with sheets and docs you will also need to enable the [Google Sheets API](https://console.cloud.google.com/apis/api/sheets.googleapis.com/) and [Google Docs API](https://console.cloud.google.com/marketplace/product/google/docs.googleapis.com) in your workspaces Enabled API and Services section.
6. [Create an OAuth Client ID](https://console.cloud.google.com/apis/credentials/oauthclient) for application type "Desktop App"
7. Download the JSON file of your client's OAuth keys
8. Note your OAuth Client ID and Client Secret from the downloaded file

### Setting up environment variables

You'll need to provide your OAuth credentials as environment variables. You have two options:

#### Option 1: Using a .env file (for development)
Create a `.env` file in the project root:
```
GDRIVE_CREDS_DIR=/path/to/store/credentials
CLIENT_ID=your-client-id-here
CLIENT_SECRET=your-client-secret-here
```

#### Option 2: Pass them directly in your MCP configuration (see Usage section below)

Make sure to build the server with either `npm run build` or `npm run watch`.

### Authentication

Next you will need to run `node ./dist/index.js` to trigger the authentication step

You will be prompted to authenticate with your browser. You must authenticate with an account in the same organization as your Google Cloud project.

Your OAuth token is saved in the directory specified by the `GDRIVE_CREDS_DIR` environment variable.

![Authentication Prompt](https://i.imgur.com/TbyV6Yq.png)

### Usage with Claude Desktop App

To use this MCP server with Claude Desktop, add the following to your Claude configuration file:

**On macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`  
**On Windows**: `%APPDATA%/Claude/claude_desktop_config.json`

```json
{
  "mcpServers": {
    "gdrive": {
      "command": "node",
      "args": ["/absolute/path/to/mcp-gdrive/dist/index.js"],
      "env": {
        "CLIENT_ID": "<YOUR_CLIENT_ID>",
        "CLIENT_SECRET": "<YOUR_CLIENT_SECRET>",
        "GDRIVE_CREDS_DIR": "/path/to/store/auth/tokens"
      }
    }
  }
}
```

Make sure to:
1. Clone this repository and run `npm install && npm run build`
2. Use absolute paths in your configuration
3. Replace `<YOUR_CLIENT_ID>` and `<YOUR_CLIENT_SECRET>` with your actual credentials

## License

This MCP server is licensed under the MIT License. This means you are free to use, modify, and distribute the software, subject to the terms and conditions of the MIT License. For more details, please see the LICENSE file in the project repository.

---

<p align="center">
  🚀 Built with love by <a href="https://dotfun.co">DotFun</a> - Where AI meets awesome! 🎉
</p>
