FROM node:18-alpine

# ✅ Install curl for healthcheck
RUN apk add --no-cache curl

WORKDIR /app
COPY . .
RUN npm install

EXPOSE 5000
CMD ["npm", "start"]

# ✅ Healthcheck with curl
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
  CMD curl --fail http://localhost:5000/health || exit 1
