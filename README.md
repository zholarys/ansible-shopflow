# Ansible ShopFlow Deployment

Automated deployment of ShopFlow e-commerce stack using Ansible.

## What it does
- Installs Docker and Docker Compose on a fresh Ubuntu server
- Clones ShopFlow repository
- Configures environment
- Starts all containers
- Initializes database and seeds products

## Usage
1. Edit inventory.ini with your server IP
2. Run: ansible-playbook -i inventory.ini deploy.yml

## Stack
- Docker + Docker Compose
- FastAPI + React + PostgreSQL + Redis + Nginx
