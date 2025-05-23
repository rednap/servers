FROM node:22.12-alpine AS builder

WORKDIR /app

COPY src/filesystem /app
COPY tsconfig.json /tsconfig.json

RUN --mount=type=cache,target=/root/.npm npm install

RUN --mount=type=cache,target=/root/.npm-production npm ci --ignore-scripts --omit-dev


FROM node:22-alpine AS release

WORKDIR /app

# Install Git
RUN apk add --no-cache git

COPY --from=builder /app/dist /app/dist
COPY --from=builder /app/package.json /app/package.json
COPY --from=builder /app/package-lock.json /app/package-lock.json

ENV NODE_ENV=production

# Install all production dependencies including simple-git
RUN npm ci --ignore-scripts --omit=dev

# If simple-git is still not available, install it specifically
RUN npm list simple-git || npm install simple-git

ENTRYPOINT ["node", "/app/dist/index.js"]