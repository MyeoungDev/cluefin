# Stage 1: Build TA-Lib
FROM python:3.11-slim as talib-builder
WORKDIR /talib
RUN apt-get update && apt-get install -y build-essential wget
RUN wget http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz && \
    tar -xzf ta-lib-0.4.0-src.tar.gz && \
    cd ta-lib && \
    ./configure --prefix=/usr && \
    make && \
    make install

# Stage 2: Build cluefin-cli with all dependencies
FROM python:3.11-slim as builder
WORKDIR /app

# Copy TA-Lib from the builder stage
COPY --from=talib-builder /usr/lib/libta_lib.so.0 /usr/lib/libta_lib.so.0
COPY --from=talib-builder /usr/lib/libta_lib.so.0.0.0 /usr/lib/libta_lib.so.0.0.0
COPY --from=talib-builder /usr/include/ta-lib/ta_defs.h /usr/include/ta-lib/ta_defs.h
COPY --from=talib-builder /usr/include/ta-lib/ta_common.h /usr/include/ta-lib/ta_common.h
COPY --from=talib-builder /usr/include/ta-lib/ta_func.h /usr/include/ta-lib/ta_func.h

# Install build dependencies
RUN apt-get update && apt-get install -y build-essential

# Install Python dependencies
COPY pyproject.toml .
RUN pip install --no-cache-dir .

# Copy the rest of the application
COPY src/ ./src/

# Stage 3: Final image
FROM python:3.11-slim
WORKDIR /app

# Copy TA-Lib from the builder stage
COPY --from=builder /usr/lib/libta_lib.so.0 /usr/lib/libta_lib.so.0
COPY --from=builder /usr/lib/libta_lib.so.0.0.0 /usr/lib/libta_lib.so.0.0.0

# Copy installed dependencies and application code from the builder stage
COPY --from=builder /usr/local/lib/python3.11/site-packages/ /usr/local/lib/python3.11/site-packages/
COPY --from=builder /app/src/ ./src/

# Set environment variables for TA-Lib
ENV LD_LIBRARY_PATH=/usr/lib

# Set the entrypoint
ENTRYPOINT ["cluefin-cli"]
