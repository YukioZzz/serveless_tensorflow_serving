FROM tensorflow/serving:latest-gpu

RUN mkdir -p /home/app

# https://forums.developer.nvidia.com/t/updating-the-cuda-linux-gpg-repository-key/212897/7
RUN sed -i '/developer\.download\.nvidia\.com\/compute\/cuda\/repos/d' /etc/apt/sources.list.d/* && sed -i '/developer\.download\.nvidia\.com\/compute\/machine-learning\/repos/d' /etc/apt/sources.list.d/*

RUN apt-get update \
    && apt-get install curl -yy

RUN echo "Pulling watchdog binary from Github." \
    && curl -sSLf https://github.com/openfaas-incubator/of-watchdog/releases/download/0.4.6/of-watchdog > /usr/bin/fwatchdog \
    && chmod +x /usr/bin/fwatchdog

WORKDIR /root/

# remove unecessery logs from S3
ENV TF_CPP_MIN_LOG_LEVEL=3

#ENV AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
#ENV AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
#ENV AWS_REGION=${AWS_REGION}
#ENV S3_ENDPOINT=${S3_ENDPOINT} 
COPY saved_model_half_plus_two_cpu	/models/saved_model_half_plus_two_cpu

# tensorflow_model_server --port=8500 --rest_api_port=8501 --model_name=${MODEL_NAME} --model_base_path=${MODEL_BASE_PATH}/${MODEL_NAME} "$@"

ENV fprocess="tensorflow_model_server --port=8500 --rest_api_port=8501 --model_name=half_plus_two --model_base_path=/models/saved_model_half_plus_two_cpu"

# Set to true to see request in function logs
ENV write_debug="true"
ENV cgi_headers="true"
ENV mode="http"
ENV upstream_url="http://127.0.0.1:8501"

#RUN touch /tmp/.lock
HEALTHCHECK --interval=5s CMD [ -e /tmp/.lock ] || exit 1
ENTRYPOINT []
CMD [ "fwatchdog" ]
