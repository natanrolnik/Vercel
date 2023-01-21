# Vercel

A Swift runtime and SDK for Vercel Serverless Functions.

## Usage

```swift
import Vercel

@main
struct App: RequestHandler {

    func onRequest(_ req: Request, context: Context) async throws -> Response {
        let greeting = EdgeConfig.default["greeting"]
        return .init(statusCode: .ok, body: "Hello, \(greeting)")
    }
}
```

## Deploy

```yaml
name: Vercel

on:
  push:
    branches:
      - main

env:
  VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
  VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
  VERCEL_TOKEN: ${{ secrets.VERCEL_TOKEN }}

jobs:
  deploy:
    runs-on: ubuntu-latest
    container: swift:5.7-amazonlinux2

    steps:
      - uses: actions/checkout@v3

      - uses: actions/cache@v3
        with:
          path: .build
          key: ${{ runner.os }}-spm-${{ hashFiles('Package.resolved') }}
          restore-keys: |
            ${{ runner.os }}-spm-

      - uses: actions/setup-node@v3
        with:
          node-version: 16

      - name: Install
        run: npm install -g vercel@latest

      - name: Deploy
        run: swift package --disable-sandbox vercel --prod
```
