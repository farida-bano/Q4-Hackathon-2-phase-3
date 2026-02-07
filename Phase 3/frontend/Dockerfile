# Multi-stage build for Next.js app
FROM node:20-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci  # Install both prod and dev dependencies for building

FROM node:20-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app

# Copy package.json for reference
COPY package*.json ./

# Install only production dependencies for the final image (as root)
RUN npm ci --only=production && npm cache clean --force

# Don't run as root user
USER node

# Copy built application from builder stage
COPY --from=builder --chown=node:node /app/.next ./.next
COPY --from=builder --chown=node:node /app/public ./public

# Expose port
EXPOSE 3000

# Start the application
ENV NODE_ENV=production
CMD ["npm", "start"]