# Stage 1: Build stage
FROM golang:1.20-alpine AS builder

# Install Git (dibutuhkan untuk go get modul)
RUN apk add --no-cache git

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN go build -o app

# Stage 2: Minimal image for runtime
FROM alpine:latest

WORKDIR /app

COPY --from=builder /app/app .

# Expose port yang digunakan oleh Gin (default 8080)
EXPOSE 8080

# Jalankan aplikasi
CMD ["./app"]
