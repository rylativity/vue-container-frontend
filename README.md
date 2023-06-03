# Docker Compose Profile Web App

The Docker Compose Profile web app is a Vue 3 application that automatically profiles a `docker-compose.yml` file and creates a Vue Card for each service with a port binding. It provides a convenient way to visualize and interact with the services defined in a `docker-compose.yml` file.

## Features

- Automatically parses and profiles a `docker-compose.yml` file.
- Generates a Vue Card component for each service with a port binding.
- Clicking on a service card navigates to the service using `localhost` as the hostname and the appropriate port.

## Getting Started

### Prerequisites

- Node.js (v14 or higher)
- npm (Node Package Manager)

### Installation

1. Clone the repository:

```bash
git clone https://github.com/your-username/docker-compose-profile.git
cd docker-compose-profile
```

2. Install dependencies:

```bash
npm install
```

### Usage

1. Place your docker-compose.yml file in the root directory of the project.

2. Start the Vue development server:

```bash
npm run serve
```

3. Open your web browser and navigate to the provided local development URL (e.g., http://localhost:8080).

4. The web app will automatically parse the docker-compose.yml file and display service cards for each service with a port binding. Clicking on a service card will navigate to the service using localhost as the hostname and the appropriate port.

### Contributing
Contributions are welcome! If you find any issues or would like to add new features, please open an issue or submit a pull request.

**Compiles and hot-reloads for development**
```
npm run serve
```

**Compiles and minifies for production**
```
npm run build
```

**Lints and fixes files**
```
npm run lint

### License
This project is licensed under the MIT License.
