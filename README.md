# MCP Server for Google Drive and Google Sheets Integration

An MCP (Model Context Protocol) server that enables AI assistants to interact with Google Drive and Google Sheets. Search files, read documents, and update spreadsheets through MCP-compatible clients like Claude Desktop.

Based on [isaacphi/mcp-gdrive](https://github.com/isaacphi/mcp-gdrive), which includes code originally developed by Anthropic, PBC from [this repo](https://github.com/modelcontextprotocol/servers/tree/main/src/gdrive), licensed under the MIT License.

## Features

- Read and search Google Drive files
- Export Google Docs to Markdown, Sheets to CSV
- Update Google Sheets cells
- OAuth 2.0 authentication flow
- Supports all Google Drive file types

## MCP Tools Available

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

## MCP Resources

The server provides access to Google Drive files via the `gdrive:///<file_id>` URI scheme.

**Automatic format conversions**:
- Google Docs → Markdown
- Google Sheets → CSV  
- Google Presentations → Plain text
- Google Drawings → PNG
- Other files → Native format

## Installation and Setup

### Prerequisites
- Node.js 18+
- Google Cloud Platform account
- Claude Desktop or other MCP-compatible client

### Google Cloud Configuration

1. [Create a new Google Cloud project](https://console.cloud.google.com/projectcreate)
2. [Enable the Google Drive API](https://console.cloud.google.com/workspace-api/products)
3. [Configure an OAuth consent screen](https://console.cloud.google.com/apis/credentials/consent) ("internal" is fine for testing)
4. Add OAuth scopes `https://www.googleapis.com/auth/drive.readonly`, `https://www.googleapis.com/auth/spreadsheets`
5. In order to allow interaction with sheets and docs you will also need to enable the [Google Sheets API](https://console.cloud.google.com/apis/api/sheets.googleapis.com/) and [Google Docs API](https://console.cloud.google.com/marketplace/product/google/docs.googleapis.com) in your workspaces Enabled API and Services section.
6. [Create an OAuth Client ID](https://console.cloud.google.com/apis/credentials/oauthclient) for application type "Desktop App"
7. Download the JSON file of your client's OAuth keys
8. Note your OAuth Client ID and Client Secret from the downloaded file

### Build and Configure

1. Clone this repository:
   ```bash
   git clone https://github.com/Dot-Fun/mcp-gdrive.git
   cd mcp-gdrive
   ```

2. Install dependencies and build:
   ```bash
   npm install
   npm run build
   ```

3. Set up environment variables in `.env`:
   ```
   GDRIVE_CREDS_DIR=/path/to/store/credentials
   CLIENT_ID=your-client-id-here
   CLIENT_SECRET=your-client-secret-here
   ```

### First-time Authentication

Run the authentication flow:
```bash
node ./dist/index.js
```

This will:
1. Open your browser for Google OAuth consent
2. Save credentials to `GDRIVE_CREDS_DIR`
3. Enable the MCP server to access your Google Drive

## Claude Desktop Configuration

Add to your Claude configuration file:
- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`  
- **Windows**: `%APPDATA%/Claude/claude_desktop_config.json`

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

Replace `/absolute/path/to/mcp-gdrive` with your actual installation path.

## Troubleshooting

- **Authentication fails**: Ensure your Google Cloud project has the Drive and Sheets APIs enabled
- **MCP client can't connect**: Verify absolute paths in your configuration
- **Permission errors**: Check that OAuth scopes include `drive.readonly` and `spreadsheets`

## Related Projects

- [Model Context Protocol Documentation](https://modelcontextprotocol.io)
- [Claude Desktop](https://claude.ai/download)
- [Google Drive API Documentation](https://developers.google.com/drive/api/v3/reference)
- [Google Sheets API Documentation](https://developers.google.com/sheets/api/reference/rest)

## License

This MCP server is licensed under the MIT License. This means you are free to use, modify, and distribute the software, subject to the terms and conditions of the MIT License. For more details, please see the LICENSE file in the project repository.

---

<p align="center">
  Built by <a href="https://dotfun.co">DotFun</a> - AI Integration Specialists
</p>
