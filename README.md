# ICCUBEA Campus Smart Navigator

ICCUBEA Campus Smart Navigator is a single-page campus wayfinding tool for conference visitors and administrators. Visitors can view the saved campus map, choose start and destination locations, and generate routes. Administrators can upload the map, place route nodes, connect roads, define named locations, and synchronize the map data with Firebase Realtime Database.

## Features

- Guest-friendly campus map and route finder
- Conference information accordion with route-ready destinations
- Interactive canvas map with node and road editing
- Named map locations linked to specific nodes
- Bidirectional route generation using breadth-first search
- Firebase Realtime Database persistence for map data and the map image
- Responsive layout for desktop and mobile screens
- Admin interface hidden from guests

## ICCUBEA Conference Destinations

The conference panel currently provides these locations:

| Event | Venue | Destination node lookup |
| --- | --- | --- |
| Registration | Ground floor, Old Reading Hall, 6th Building | `6th Building` |
| Inauguration | 5th floor, Architecture Building | `Architecture Building` |
| Valedictory Ceremony | Seminar Hall, 9th Building | `Seminar Hall` and `9th Building` |

Selecting **Use as destination** fills the route finder's destination field using the matching saved location.

## Running Locally

The project does not require a build step or package installation.

1. Clone the repository.
2. Open `ICCUBEA.html` in a modern browser, or serve the folder with a local static server.

For example:

```bash
python3 -m http.server 8000
```

Then open [http://localhost:8000/ICCUBEA.html](http://localhost:8000/ICCUBEA.html).

Opening the file directly with a `file://` URL works for the interface, but a local server is recommended when testing Firebase or browser security behavior.

## User Access

The opening screen exposes one option: **Continue as Guest**.

For the current administrator workflow, press:

- Windows/Linux: `Ctrl + Shift + A`
- macOS: `Cmd + Shift + A`

This switches the client-side session to administrator mode and reveals the map editing tools. There is currently no Firebase Authentication or server-side authorization, so this shortcut is not a secure production access control mechanism.

## Administrator Workflow

1. Enter administrator mode with the keyboard shortcut above.
2. Upload a top-view campus image in **Setup Map**.
3. Choose **Add Node (Junction)** and click map points to create nodes.
4. Choose **Connect Nodes (Road)** and click two nodes to connect them. Roads are stored in both directions for routing.
5. Choose **Select Node for Location**, click a node, enter a location name, and select **Save Location**.
6. Use **Sync All to Firebase** to save the current nodes, roads, locations, and map image.

Location names should contain the conference destination terms used by the conference buttons, especially `6th Building`, `Architecture Building`, `Seminar Hall`, or `9th Building`.

## Route Finder Workflow

1. Select a starting location in **From**.
2. Select a destination in **To**, or choose a conference destination button.
3. Select **Generate Route**.
4. Use **Clear Route** to remove the current route and selections.

The route finder searches the saved road network. A route cannot be generated unless both locations are saved and their nodes are connected by roads.

## Firebase Setup

The Firebase compatibility SDKs are loaded from Google's CDN in `ICCUBEA.html`. The Firebase configuration object is located near the bottom of the file.

To use another Firebase project:

1. Create a Firebase project.
2. Add a Web App in Firebase Project Settings.
3. Enable Realtime Database.
4. Replace the `firebaseConfig` object in `ICCUBEA.html` with the new project's configuration.
5. Configure Realtime Database rules appropriate for your deployment.

The application uses these Realtime Database paths:

```text
mapData/
  nodes/
  roads/
  locations/
mapImage/
```

The stored data shape is:

```js
{
  nodes: [{ id, x, y }],
  roads: [{ fromId, toId }],
  locations: [{ name, nodeId }]
}
```

## Deployment

Because the project is static, it can be deployed through GitHub Pages or any static hosting provider. For GitHub Pages, enable Pages for the repository's `main` branch and open the generated site at:

```text
https://yuvraj-044.github.io/Iccubea/ICCUBEA.html
```

If the hosting setup expects `index.html`, either configure the host to serve `ICCUBEA.html` as the entry file or add a separate redirect/index file.

## Project Structure

```text
ICCUBEA/
├── ICCUBEA.html   # Complete application: markup, styles, and JavaScript
└── README.md      # Project documentation
```

## Important Security Note

The current application uses a keyboard shortcut for the admin role and does not authenticate users with Firebase Auth. Anyone who can access the page can inspect the source and invoke the admin flow. Before production use, add Firebase Authentication and database security rules that restrict map writes to authorized administrators.

