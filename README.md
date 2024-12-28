
# Configuração de TensorFlow com GPU em Docker

Este guia documenta os passos para configurar um ambiente TensorFlow com suporte a GPU em um container Docker.

## Pré-requisitos

1. **Drivers NVIDIA** instalados no host:
   ```bash
   nvidia-smi
   ```
   Deve exibir informações sobre sua GPU e driver.

2. **NVIDIA Container Toolkit** configurado:
   ```bash
   sudo apt update
   sudo apt install -y nvidia-container-toolkit
   sudo systemctl restart docker
   ```

3. **Docker instalado** no sistema.

---

## Passos para Configuração

1. **Baixar a imagem Docker oficial da NVIDIA com TensorFlow:**
   ```bash
   docker pull nvcr.io/nvidia/tensorflow:23.09-tf2-py3
   ```

2. **Executar o container com suporte a GPU:**
   ```bash
   docker run --rm --gpus all --ipc=host --ulimit memlock=-1 --ulimit stack=67108864 -it nvcr.io/nvidia/tensorflow:23.09-tf2-py3 bash
   ```

3. **Testar se o TensorFlow detecta a GPU:**
   Dentro do container, execute:
   ```bash
   python3 -c "import tensorflow as tf; print(tf.config.list_physical_devices('GPU'))"
   ```
   A saída esperada será algo como:
   ```
   [PhysicalDevice(name='/physical_device:GPU:0', device_type='GPU')]
   ```

4. **Opcional: Mapear um diretório do host para o container:**
   Para persistir arquivos entre o host e o container:
   ```bash
   docker run --rm --gpus all --ipc=host --ulimit memlock=-1 --ulimit stack=67108864 -v $(pwd):/workspace -it nvcr.io/nvidia/tensorflow:23.09-tf2-py3 bash
   ```

5. **Instalar pacotes adicionais no container (se necessário):**
   Por exemplo:
   ```bash
   pip install numpy pandas matplotlib
   ```

---

## Notas Importantes

- Use as flags `--ipc=host`, `--ulimit memlock=-1` e `--ulimit stack=67108864` para evitar problemas de memória compartilhada no TensorFlow.
- Certifique-se de que os drivers NVIDIA e o NVIDIA Container Toolkit estão configurados corretamente no host.
- Este guia foi testado com:
  - **Docker**: Última versão estável
  - **TensorFlow**: Versão 2.13.0
  - **CUDA**: Versão 12.4
  - **GPU**: NVIDIA GTX 1050 Ti

---

## Comandos Úteis

- **Listar GPUs disponíveis no host:**
  ```bash
  nvidia-smi
  ```

- **Verificar se o Docker reconhece GPUs:**
  ```bash
  docker run --rm --gpus all nvidia/cuda:12.2.0-runtime-ubuntu22.04 nvidia-smi
  ```

- **Persistir configurações do container em uma imagem personalizada:**
  ```bash
  docker commit <container_id> my-tf-gpu-env
  ```

---

## Autor

Documentação baseada em passos bem-sucedidos para configurar TensorFlow com GPU em Docker.