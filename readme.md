
# docker-compose com um gitlab + gitlab-ci com o registry embutido e um runner docker

## Registrando o runner
```bash
docker run -it --network docker-compose-gitlab-ci_default -v `pwd`/conf/runner:/etc/gitlab-runner gitlab/gitlab-runner register
# informar:
# host: gitlab
# token: o token gerado pelo gitlab na aba de adição de um runner
# descrição para o runner: qualquer coisa[compose-runner-1]
# tags separadas por virgula: qualquer coisa para fazer o filtro de onde rodar[docker]
# executor: docker
```


### Config pós register do runner
```toml
# vai ser o arquivo que vai ficar no diretorio conf/rrunner/config.toml

[[runners]]
  name = "compose-runner-1"
  url = "http://gitlab/"
  token = "6VDMQ-ft-JfeRtzf_pSV" # vai ser substituido com o gerado pelo register !
  executor = "docker"
  [runners.custom_build_dir]
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
    [runners.cache.azure]
  [runners.docker]
    tls_verify = false
    network_mode = "host" #para funcionar nos containers que vão rodar dentro do dind, vai usar a rede do container dind e não bridge, que iria seria separado do docker-compose
    image = "alpine"
    privileged = false
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/cache"]
    shm_size = 0
```