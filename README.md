# Order Processor with Dapr

A simple .NET 6 microservice that demonstrates event-driven architecture using Dapr (Distributed Application Runtime) for handling order processing through publish-subscribe messaging patterns.

## Overview

This application serves as an order processor that subscribes to order events published to a Dapr pub/sub component. When an order is received, it processes the order data and logs the information to the console.

## Features

- **Event-Driven Architecture**: Uses Dapr pub/sub for decoupled messaging
- **Cloud Events Support**: Handles CloudEvents format for standardized event processing
- **Containerized**: Docker support for easy deployment and scaling
- **Minimal API**: Built with .NET 6 minimal APIs for lightweight performance

## Architecture

```
Publisher → Dapr Sidecar → Pub/Sub Component → Dapr Sidecar → Order Processor
```

The application subscribes to the `orders` topic on the `orderpubsub` pub/sub component and processes incoming order events.

## Prerequisites

- [.NET 6 SDK](https://dotnet.microsoft.com/download/dotnet/6.0)
- [Dapr CLI](https://docs.dapr.io/getting-started/install-dapr-cli/)
- [Docker](https://www.docker.com/get-started) (optional, for containerized deployment)

## Project Structure

```
├── Program.cs                          # Main application entry point
├── order-processor.csproj              # Project file with dependencies
├── Dockerfile                          # Docker configuration
├── appsettings.json                    # Application configuration
├── appsettings.Development.json        # Development-specific settings
└── Properties/
    └── launchSettings.json             # Launch profiles
```

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/nelsonApraez/order-processor-dapr.git
cd order-processor-dapr
```

### 2. Restore Dependencies

```bash
dotnet restore
```

### 3. Run with Dapr

To run the application with Dapr sidecar:

```bash
dapr run --app-id order-processor --app-port 7001 --dapr-http-port 3500 -- dotnet run
```

### 4. Alternative: Run Directly (Development)

For development without Dapr:

```bash
dotnet run
```

## Configuration

### Application Settings

The application uses standard .NET configuration with the following key settings:

- **Port**: 7001 (configured in Dockerfile)
- **Environment**: Development/Production
- **Logging**: Configurable log levels

### Dapr Configuration

- **App ID**: `order-processor`
- **Pub/Sub Component**: `orderpubsub`
- **Topic**: `orders`
- **Port**: 7001

## API Endpoints

### POST /orders

Processes incoming order events from the Dapr pub/sub subscription.

**Request Body:**
```json
{
  "orderId": 12345
}
```

**Response:**
```json
{
  "orderId": 12345
}
```

## Data Models

### Order

```csharp
public record Order([property: JsonPropertyName("orderId")] int OrderId);
```

## Docker Support

### Build Docker Image

```bash
docker build -t order-processor .
```

### Run with Docker

```bash
docker run -p 7001:7001 order-processor
```

## Development

### Dependencies

- **Dapr.AspNetCore** (v1.8.0): Dapr integration for ASP.NET Core
- **Swashbuckle.AspNetCore** (v6.2.3): API documentation and testing

### Key Components

1. **CloudEvents Middleware**: Handles CloudEvents format from Dapr
2. **Subscribe Handler**: Maps Dapr subscription endpoints
3. **Topic Attribute**: Declares subscription to specific pub/sub topics

## Testing

### Send Test Events

You can test the application by publishing events to the Dapr pub/sub component:

```bash
curl -X POST http://localhost:3500/v1.0/publish/orderpubsub/orders \
  -H "Content-Type: application/json" \
  -d '{"orderId": 12345}'
```

## Deployment

### Local Development

1. Ensure Dapr is initialized: `dapr init`
2. Run with Dapr: `dapr run --app-id order-processor --app-port 7001 -- dotnet run`

### Production

1. Build and push Docker image to your registry
2. Deploy with your orchestration platform (Kubernetes, Docker Compose, etc.)
3. Configure Dapr pub/sub components for your environment

## Monitoring and Observability

The application includes:

- Console logging for order processing events
- Standard .NET logging configuration
- Dapr built-in observability features

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## License

This project is licensed under the terms specified in the LICENSE file.

## Resources

- [Dapr Documentation](https://docs.dapr.io/)
- [.NET 6 Documentation](https://docs.microsoft.com/en-us/dotnet/core/whats-new/dotnet-6)
- [Dapr Pub/Sub Building Block](https://docs.dapr.io/developing-applications/building-blocks/pubsub/)
- [CloudEvents Specification](https://cloudevents.io/)
