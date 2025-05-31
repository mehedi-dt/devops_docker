## Multi-App Infrastructure Repository

This repository is a collection of containerized applications and their configurations. Each application is self-contained in its own directory and follows a modular and scalable structure.

## Repository Structure

```
├── README.md                 # Root README (this file)
└── php-app/                  # PHP application
    ├── Dockerfile            # Main Dockerfile for the PHP app
    ├── base-image/           # Base Docker image definition
    │   └── Dockerfile
    └── config/               # Configuration files
        ├── nginx.conf
        └── php-custom.ini
```

## Applications

Each application resides in its own directory and contains:
- Dockerfile(s)
- Configuration files
- Supporting assets
- Its own `README.md` with app-specific documentation