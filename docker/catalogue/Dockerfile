FROM golang:1.16-buster AS build

WORKDIR /app
COPY go.mod ./
COPY go.sum ./
RUN go env -w GOPROXY=direct && go mod download
COPY . .

RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o /catalogue cmd/cataloguesvc/main.go
RUN chmod +x /catalogue && chown 65534:65534 /catalogue && \
        setcap CAP_NET_BIND_SERVICE=+eip /catalogue && \
        chown -R 65534:65534 images



FROM debian:11

WORKDIR /
COPY --from=build /catalogue /catalogue
COPY --from=build /app/images /images

USER 65534

CMD ["/catalogue", "-port=8080"]

EXPOSE 8080