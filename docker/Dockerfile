ARG GO_VERSION=1.16

FROM golang:${GO_VERSION}-alpine AS dev

RUN apk update && \
    apk add curl && \
    curl -sSfL https://raw.githubusercontent.com/cosmtrek/air/master/install.sh | sh -s


WORKDIR "/var/app"
COPY ../../app .

ENV GO111MODULE="on" \
    CGO_ENABLED=0 \
    GOOS=linux \
    GOFLAGS="-mod=vendor"

EXPOSE 8080
ENTRYPOINT ["air"]



FROM dev as build

WORKDIR "/var/app"
RUN (([ ! -d "/var/app/vendor" ] && go mod download && go mod vendor) || true)
RUN go build -ldflags="-s -w" -mod vendor -o bin main.go
RUN chmod +x bin



FROM scratch AS prod
    
WORKDIR "/var/app"
COPY --from=build "/var/app/bin" "/var/app"

EXPOSE 8080
ENTRYPOINT ["/var/app/bin"]
