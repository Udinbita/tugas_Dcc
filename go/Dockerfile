
FROM golang:1.21-alpine AS builder

WORKDIR /app


COPY go.mod go.sum ./


RUN go mod download


COPY main.go .


RUN CGO_ENABLED=0 GOOS=linux go build -o main .


FROM alpine:latest

WORKDIR /app


COPY --from=builder /app/main .
COPY --from=builder /app/go.mod .
COPY --from=builder /app/go.sum .

EXPOSE 8080


CMD ["./main"]