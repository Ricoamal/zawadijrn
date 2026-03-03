# Build stage
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./
COPY .env.production ./
RUN npm install

COPY . .
RUN npx prisma generate
RUN npm run build

# Production stage
FROM node:20-alpine

WORKDIR /app

# Install dumb-init to handle signals properly
RUN apk add --no-cache dumb-init

COPY --from=builder /app/package*.json ./
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/prisma ./prisma
COPY --from=builder /app/.env.production .env.production

EXPOSE 5000

# Use dumb-init to properly handle signals
# Remove prisma migrate - it's managed by Supabase CLI
# Just start the server directly
ENTRYPOINT ["/usr/sbin/dumb-init", "--"]
CMD ["node", "dist/index.js"]
