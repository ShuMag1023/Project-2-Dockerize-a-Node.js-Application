# Dockerize-a-Node.js-Application


Great! Let's dive into the details of dockerizing a Node.js application. This project involves setting up a Node.js application, writing a `Dockerfile` to containerize it, and using Ansible to automate the deployment.

### Project Outline

1. **Setup GitHub Repository:**
   - Create a GitHub repository for a simple Node.js application.
   - Add the Node.js application code and a `Dockerfile`.

2. **Write Dockerfile:**
   - Create a `Dockerfile` to containerize the Node.js application.

3. **Ansible Playbook for Deployment:**
   - Write an Ansible playbook to pull the code from the GitHub repository, build the Docker image, and deploy the Docker container.

### Detailed Steps

#### Step 1: Setup GitHub Repository

1. Create a new repository on GitHub (e.g., `node-docker-app`).
2. Initialize a simple Node.js application in the repository.

**Sample Node.js Application:**

- `app.js`:
    ```javascript
    const express = require('express');
    const app = express();
    const port = 3000;

    app.get('/', (req, res) => {
        res.send('Hello World!');
    });

    app.listen(port, () => {
        console.log(`Example app listening at http://localhost:${port}`);
    });
    ```

- `package.json`:
    ```json
    {
      "name": "node-docker-app",
      "version": "1.0.0",
      "description": "A simple Node.js application to be dockerized",
      "main": "app.js",
      "scripts": {
        "start": "node app.js"
      },
      "dependencies": {
        "express": "^4.17.1"
      }
    }
    ```

#### Step 2: Write Dockerfile

Create a `Dockerfile` in the root of your repository.

**Dockerfile:**
```dockerfile
# Use the official Node.js image as a base
FROM node:14

# Set the working directory inside the container
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# Expose the application port
EXPOSE 3000

# Start the application
CMD ["npm", "start"]
```

#### Step 3: Ansible Playbook for Deployment

Create an Ansible playbook (`deploy.yml`) to automate the deployment process.

**Ansible Playbook (`deploy.yml`):**
```yaml
---
- hosts: webservers
  vars:
    repo_url: "https://github.com/your-username/node-docker-app.git"
    repo_dest: "/tmp/node-docker-app"
    docker_image: "node-docker-app"

  tasks:
    - name: Install required packages
      apt:
        name: "{{ item }}"
        state: present
      with_items:
        - git
        - docker.io

    - name: Clone the repository
      git:
        repo: "{{ repo_url }}"
        dest: "{{ repo_dest }}"
        version: HEAD

    - name: Build the Docker image
      command: docker build -t {{ docker_image }} .
      args:
        chdir: "{{ repo_dest }}"

    - name: Run Docker container
      docker_container:
        name: node_app
        image: "{{ docker_image }}"
        state: started
        restart_policy: always
        ports:
          - "3000:3000"
```

#### Step 4: Define Inventory File

Create an inventory file (`hosts.ini`) to define the target server(s).

**Inventory File (`hosts.ini`):**
```ini
[webservers]
your-server-ip ansible_user=your-ssh-username ansible_ssh_private_key_file=/path/to/your/private/key
```

#### Step 5: Run the Ansible Playbook

Execute the Ansible playbook to deploy the Node.js application.

**Run the Playbook:**
```sh
ansible-playbook -i hosts.ini deploy.yml
```

### Conclusion

By following these steps, you will have dockerized a Node.js application, stored it in a GitHub repository, and automated its deployment using Ansible. This project helps you gain practical experience with containerization and deployment automation, essential skills in modern DevOps practices.
