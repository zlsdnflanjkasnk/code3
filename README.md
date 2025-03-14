# 1. set up dockerfile
# Step 1: Use an official Node.js runtime as a parent image
FROM node:18

# Step 2: Set the working directory inside the container
WORKDIR /bao_yuwei_ui_garden_checks

# Step 3: Copy package.json and yarn.lock into the container
COPY package.json yarn.lock ./

# Step 4: Install dependencies using Yarn
RUN yarn install


# Step 5: Copy the rest of the project files into the container
COPY . .

# Step 6: Build the production version of the React app
RUN yarn build

# Step 7: Expose port 8018 for the app to be accessed
EXPOSE 8018

# Step 8: Command to run the app in production mode
CMD ["yarn", "start"]

# 2 install husky
npx husky-init
npm install --save-dev husky

# 3 add pre-commit hook
npx husky add .husky/pre-commit "npx lint-staged"

# 4 Configure lint-staged: Add lint-staged configuration to package.json:
"lint-staged": {
  "*.js": ["eslint --fix", "prettier --write"],
  "*.ts": ["eslint --fix", "prettier --write"]
}

# 5 create github actions for CI/CD
Create a .github/workflows/ci.yml file:
name: CI/CD Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Set up Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '16'

    - name: Install dependencies
      run: npm install

    - name: Run ESLint, Prettier, and tests
      run: |
        npm run lint
        npm run prettier -- --check
        npm test

# 6 build docker image and container
docker build -t bao_yuwei_coding_assignment13 .
docker run -p 8018:8018 bao_yuwei_coding_assignment13

# 7 try the implemented test and push to github
git commit -m "Test"
git push origin main



