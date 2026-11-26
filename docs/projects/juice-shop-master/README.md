# Juice Shop Master

This project documents the exploitation of selected OWASP Juice Shop challenges.
Each challenge is explained step by step and demonstrated in a short video.
The goal is to understand common web vulnerabilities and their impact.

:::warning
This documentation is for educational purposes only. Do not apply these 
techniques to systems without explicit permission.
:::

## Table of Contents
- [Quickstart](#quickstart)
- [Challenges](#challenges)
  - [Login Admin](./login-admin/README.md)
  - [DOM XSS](./dom-xss/README.md)

## Quickstart
1. Start Juice Shop locally:
   `docker run --rm -p 3000:3000 bkimminich/juice-shop`
2. Open `http://localhost:3000`
3. Follow the steps in the challenge documentation below.

## Challenges

| Challenge | Category | Video | Documentation |
|-----------|----------|-------|---------------|
| Login Admin | Injection | [Video](LINK) | [Docs](./login-admin/README.md) |
| DOM XSS | XSS | [Video](LINK) | [Docs](./dom-xss/README.md) |