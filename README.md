# .github/workflows/ci.yml
name: 🔧 CI Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build-and-test:
    name: 🧪 Test on Node ${{ matrix.node }}
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node: [16, 18, 20]

    steps:
    - name: 📥 Checkout repositor
      uses: actions/checkout@v3

    - name: 🛠 Setup Node.js ${{ matrix.node }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node }}
        cache: 'npm'

    - name: 📦 Install dependencies
      run: npm ci

    - name: 🔍 Run linter
      run: npm run lint

    - name: 🧪 Run tests
      run: npm test

    - name: 🏗 Build project
      run: npm run build

    - name: ✅ Upload test results (optional)
      if: success()
      uses: actions/upload-artifact@v3
      with:
        name: test-results-${{ matrix.node }}
        path: ./test-results/

    - name: ❌ Notify on failure
      if: failure()
      run: echo "❗ CI failed on Node ${{ matrix.node }}"
