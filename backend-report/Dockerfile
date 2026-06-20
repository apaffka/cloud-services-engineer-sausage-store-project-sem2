FROM golang:1.22-alpine AS builder

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux go build -o backend-report .

FROM alpine:3.19

RUN apk add --no-cache ca-certificates \
    && addgroup -S app \
    && adduser -S app -G app

WORKDIR /app

COPY --from=builder /app/backend-report /app/backend-report

USER app

EXPOSE 8080

CMD ["/app/backend-report"]