# local/Dockerfile — the Tilt / kind inner loop
#
# Layout-agnostic on purpose: this overlay retrofits an EXISTING application, so it copies the whole
# repo and runs the application's OWN build command rather than assuming a module name, a crate, a
# src/ layout or an output path. `build_command` and `runtime_artifact` are answers; their defaults
# match what this language's p6m service archetype produces, so a greenfield-shaped repo needs
# neither.

FROM mcr.microsoft.com/dotnet/sdk:9.0 AS builder
WORKDIR /src
COPY . .
RUN {{ build_command }}

FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS runtime
WORKDIR /app
USER root
RUN apt-get update && apt-get install -y --no-install-recommends ca-certificates && \
    rm -rf /var/lib/apt/lists/*
USER app
COPY --from=builder --chown=app:app /app/publish .
ENV ASPNETCORE_URLS=""
EXPOSE {{ service_port }}
EXPOSE {{ management_port }}
ENTRYPOINT ["dotnet", "{{ runtime_artifact }}"]
