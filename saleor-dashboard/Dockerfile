# Step 1: Builder Stage
FROM node:18-alpine AS builder

# Install bash for compatibility
RUN apk add --no-cache bash

# Set working directory
WORKDIR /app

# Step 2: Copy and install dependencies
COPY package.json package-lock.json ./
ENV CI=1
RUN npm ci --legacy-peer-deps

# Step 3: Copy application files
COPY . .

# Step 4: Set up environment variables
ARG API_URL
ARG APP_MOUNT_URI
ARG APPS_MARKETPLACE_API_URL
ARG APPS_TUNNEL_URL_KEYWORDS
ARG STATIC_URL
ARG SKIP_SOURCEMAPS
ARG LOCALE_CODE

# Define default values for runtime environment variables
ENV API_URL=${API_URL:-http://localhost:8000/graphql/}
ENV APP_MOUNT_URI=${APP_MOUNT_URI:-/dashboard/}
ENV APPS_MARKETPLACE_API_URL=${APPS_MARKETPLACE_API_URL:-https://apps.saleor.io/api/v2/saleor-apps}
ENV APPS_TUNNEL_URL_KEYWORDS=${APPS_TUNNEL_URL_KEYWORDS}
ENV STATIC_URL=${STATIC_URL:-/dashboard/}
ENV SKIP_SOURCEMAPS=${SKIP_SOURCEMAPS:-true}
ENV LOCALE_CODE=${LOCALE_CODE:-EN}

# Step 5: Build the application
RUN npm run build

# Step 6: Runner Stage
FROM nginx:stable-alpine AS runner

# Set working directory
WORKDIR /app

# Step 7: Copy built files from builder
COPY --from=builder /app/build/ /app/
COPY ./nginx/default.conf /etc/nginx/conf.d/default.conf
COPY ./nginx/replace-env-vars.sh /docker-entrypoint.d/50-replace-env-vars.sh

# Step 8: Expose port and run
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
