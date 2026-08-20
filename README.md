# iModel Locks Viewer / Releaser

A web-based tool for viewing and managing iModel briefcase locks per the iModels v2 API. This single-page application provides a user-friendly interface to query lock status and release locks for specific briefcases.

## Features

- **View Locks**: Fetch and display all locks for a given iModel, organized by briefcase
- **Release Locks**: Release all locks or only exclusive locks for a selected briefcase
- **Briefcase Details**: View briefcase metadata and owner information
- **Environment Switching**: Toggle between Production and QA environments
- **Persistent Storage**: Optionally remember your access token and iModel ID in browser storage
- **Raw Mode**: Edit lock release payloads as raw JSON for advanced use cases
- **Clipboard Integration**: Paste instance IDs directly into lock release payloads

## Getting Started

### Requirements

- A valid iTwin Platform access token with `itwin-platform` scope
- For releasing locks, your token should include the `imodels_manage` scope
- An iModel UUID

### Usage

1. **Open** `index.html` in a modern web browser
2. **Enter** your access token in the "Access Token (Bearer)" field
3. **Enter** the iModel ID (UUID)
4. **Select** your environment (Production or QA)
5. **Click** "Load Locks" to fetch locks for all briefcases in the iModel

#### Viewing Locks

Once locks are loaded:
- The **Briefcase Id** dropdown is populated with all briefcases that have locks
- **Briefcase Details** section displays owner information and metadata
- **Status / Log** shows the request/response history

#### Releasing Locks

Select a briefcase and choose a release mode:

- **Release all locks for selected briefcase**: Sets `lockLevel = "none"` for all locked objects
- **Release exclusive locks only**: Sets `lockLevel = "none"` only for objects with `lockLevel = "exclusive"`
- **Edit raw request body**: Manually edit the PATCH payload as JSON

Then click **Send Update (PATCH)** to release the locks.

### Optional Settings

- **Remember Access Token and iModel Id**: Check this box to persist your token and iModel ID in browser storage (useful for local/trusted machines)
- **Paste Instance Ids from Clipboard**: In raw mode, paste newline-separated instance IDs to populate the request body

## API Endpoints

The tool uses the **iModels v2 API**:

- `GET /imodels/{id}/locks` — Retrieve all locks for an iModel
- `PATCH /imodels/{id}/locks` — Update lock state (release locks)
- `GET /imodels/{id}/briefcases/{briefcaseId}` — Fetch briefcase details
- `GET /users/{userId}` — Fetch user/owner information (via link from briefcase response)

**Base URLs:**
- Production: `https://api.bentley.com`
- QA: `https://qa-api.bentley.com`

See the [iModels v2 API Documentation](https://developer.bentley.com/apis/imodels-v2/) for more details.

## Request/Response Format

### Request Body (PATCH)

```json
{
  "briefcaseId": 123,
  "changesetId": "(optional)",
  "lockedObjects": [
    {
      "lockLevel": "none",
      "objectIds": ["id1", "id2", ...]
    }
  ]
}
```

### Response Format

```json
{
  "locks": [
    {
      "briefcaseId": 123,
      "changesetId": "...",
      "lockedObjects": [
        {
          "lockLevel": "exclusive",
          "objectIds": ["..."]
        },
        {
          "lockLevel": "shared",
          "objectIds": ["..."]
        }
      ]
    }
  ]
}
```

## Security Notes

⚠️ **Important**: This page handles your OAuth access token.

- Use this tool only on a **trusted machine**
- Avoid sharing screenshots or logs that contain your token
- The token is sent directly to Bentley's iTwin Platform API
- Optionally disable persistent storage if using a shared computer

## Browser Compatibility

- Modern browsers with ES6+ support
- Requires `fetch` API and `localStorage`
- Clipboard API required for the "Paste Instance Ids" feature (graceful fallback if unavailable)

## Styling

The UI uses:
- **Font**: Space Grotesk (headings) and JetBrains Mono (code)
- **Color Scheme**: Custom CSS variables with light, warm palette
- **Animations**: Smooth card reveals and button transitions
- **Responsive Layout**: Flexbox with mobile-friendly scaling

## Development

The entire application is contained in a single `index.html` file:
- HTML structure
- Inline CSS styling
- Inline JavaScript (IIFE pattern for scoping)

No build step or external dependencies required—just open the file in a browser.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Access token is required" | Ensure your token is entered and not empty |
| "No locks returned for this iModel" | The iModel may have no active locks, or your token may lack permissions |
| Briefcase details show "(not loaded)" | Check network requests in browser DevTools; the user details endpoint may have failed |
| Clipboard paste unavailable | This feature only works in secure contexts (HTTPS or `file://`) |
| "Payload is not valid JSON" | Verify the JSON syntax in the request body textarea |

## License

Internal iTwin/Bentley tool.

## Support

For questions or issues, refer to:
- [iModels v2 API Documentation](https://developer.bentley.com/apis/imodels-v2/)
- [iTwin Platform Documentation](https://www.itwinjs.org/)
