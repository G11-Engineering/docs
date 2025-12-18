# Microservices

## Overview

The G11 blog platform is built using a modular microservice architecture with the following services:

### Core Services
- **category-service** → Manages blog categories and tags
- **comment-service** → Handles comments on blog posts
- **content-service** → Manages the blog posts themselves
- **media-service** → Handles media files, integrated with AWS S3
- **user-service** → Manages user accounts and authentication

### Frontend
- **frontend-service** → Next.js React application for user interface

## Tech Stack & Development Environment

### Frontend
- Next.js, React, TypeScript/JavaScript
- Responsive UI with modern JavaScript frameworks
- Jest testing framework

### Backend / Services
- Node.js runtime environment
- TypeScript for type safety
- Express.js or similar HTTP frameworks
- PostgreSQL for data persistence

### Version Control / CI/CD
- Git for version control
- GitHub for repository hosting
- GitHub Actions for CI/CD automation
- .trivyignore for security scan management
- Multi-architecture builds (x86 and ARM)

### Containerization
- Docker for containerizing microservices
- docker-compose for local development
- Docker Hub for image registry
- Multi-stage builds for optimized images

### Development Approach
- Modular microservice development
- Configurable environments (dev, staging, prod)
- CI/CD pipelines for automated build & testing
- Workflow and merge conflict resolution

## Build & Configuration

### Configuration Management
- Environment variables for flexibility
- Configurable for different deployment environments
- Database configuration per service
- Service discovery and communication

### Build Issues Resolution
- Fixed build issues across services
- Standardized build process
- Dockerfile optimization
- Dependencies management

## Service Communication

### Inter-Service Communication
- RESTful APIs for service-to-service communication
- mTLS enforcement for security
- API gateway handling external requests
- Service discovery via Kubernetes DNS

### Authentication & Authorization
- Asgardeo integration for SSO
- JWT token validation
- Role-based access control (Admin, Editor, Reader)
- Authorization policies at gateway level

See also: [Architecture](ARCHITECTURE.md) for security details, [Kubernetes](KUBERNETES.md) for deployment topology