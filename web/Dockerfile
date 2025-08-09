    # Stage 1: Build the Next.js application
    FROM node:20-alpine AS builder

    WORKDIR /app

    COPY package.json yarn.lock* pnpm-lock.yaml* ./
    RUN \
      if [ -f "yarn.lock" ]; then yarn install --frozen-lockfile; \
      elif [ -f "pnpm-lock.yaml" ]; then pnpm install --frozen-lockfile; \
      else npm install --frozen-lockfile; \
      fi

    COPY . .

    ENV NEXT_TELEMETRY_DISABLED 1
    RUN npm run build

    # Stage 2: Create the production image
    FROM node:20-alpine AS runner

    WORKDIR /app

    ENV NODE_ENV production
    # Set the standalone flag to enable tracing and reduce deployment size
    ENV NEXT_PRIVATE_STANDALONE=true

    COPY --from=builder /app/.next/standalone ./
    COPY --from=builder /app/.next/static ./.next/static
    COPY --from=builder /app/public ./public

    EXPOSE 3000

    CMD ["node", "server.js"]