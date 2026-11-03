FROM dhi.io/golang:1-alpine AS builder

WORKDIR /app

COPY --chmod=0644 go.mod go.sum ./
RUN go mod download

COPY --chmod=0644 . .
RUN CGO_ENABLED=0 go build -o devboard-backend .

FROM dhi.io/golang:1-alpine

WORKDIR /app

COPY --from=builder --chown=65532:65532 /app/devboard-backend ./devboard-backend

RUN chmod +x ./devboard-backend

USER 65532:65532

EXPOSE 8080

ENTRYPOINT ["./devboard-backend"]
