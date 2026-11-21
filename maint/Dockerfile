FROM ubuntu:18.04 AS builder

RUN --mount=type=bind,target=/src <<'EOF_BUILD' bash -euxo pipefail
export DEBIAN_FRONTEND=noninteractive
apt-get update
apt-get install -y cmake gcc git libc6-dev make tar

case "$(uname -m)" in
  x86_64) arch=amd64 ;;
  aarch64) arch=arm64 ;;
  *) echo "Unsupported architecture: $(uname -m)" >&2; exit 1 ;;
esac

cp -a /src /work
mkdir /build
cd /build
cmake /work
cmake --build . --target tini
mkdir /tini-linux-$arch
cp tini /tini-linux-$arch/tini
tar --owner=0 --group=0 --numeric-owner -C / -czf /tini-linux-$arch.tar.gz tini-linux-$arch
EOF_BUILD

FROM scratch
COPY --from=builder /tini-linux-*.tar.gz /
