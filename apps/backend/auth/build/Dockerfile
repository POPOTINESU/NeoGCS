FROM golang:1.23.3 AS base
WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

FROM base AS development
COPY . .
RUN go install github.com/air-verse/air@latest && \
    go install -tags 'postgres' github.com/golang-migrate/migrate/v4/cmd/migrate@latest

ENTRYPOINT ["/app/entrypoint.sh"]
CMD ["air", "-c", "/app/build/.air.toml"]

FROM base AS build
COPY . .
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o /app/app .

FROM alpine:latest AS production
WORKDIR /root/
COPY --from=build /app/app .
ENTRYPOINT ["/app/entrypoint.sh"]
CMD ["./app"]