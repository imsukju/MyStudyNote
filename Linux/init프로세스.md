### **도커의 Init 프로세스란?**

도커의 Init 프로세스는 컨테이너 내에서 실행되는 첫 번째 프로세스(프로세스 ID 1, 즉 `PID 1`)를 말합니다. 이는 컨테이너 내부에서 모든 다른 프로세스의 부모 프로세스 역할을 하며, 컨테이너의 정상적인 실행 및 종료를 관리합니다.

---

### **Linux의 Init 프로세스와 관계**

Linux 시스템에서 **Init 프로세스**는 부팅 시 가장 먼저 실행되는 프로세스(`PID 1`)로, 시스템의 다른 모든 프로세스를 생성하고 관리하는 역할을 합니다. 도커의 Init 프로세스는 이와 유사하게 컨테이너 내부에서 시작하여 컨테이너 내 모든 작업의 시작점이 됩니다.

---

### **도커 Init 프로세스의 역할**

1. **자식 프로세스 관리**
    - 컨테이너 내부에서 실행되는 프로세스가 비정상적으로 종료되면 고아 프로세스가 발생합니다.
    - Init 프로세스는 이러한 고아 프로세스를 처리하고, 좀비 프로세스가 발생하지 않도록 `wait()` 시스템 호출을 통해 자식 프로세스의 종료 상태를 정리합니다.
2. **컨테이너 종료 관리**
    - 컨테이너가 종료될 때 Init 프로세스는 모든 자식 프로세스를 종료하고, 리소스를 정리합니다.
3. **신호 전달**
    - 컨테이너는 `SIGTERM`과 같은 종료 신호를 받을 때, 이를 Init 프로세스를 통해 적절히 처리합니다.
    - Init 프로세스가 없다면, 신호가 컨테이너 내부의 애플리케이션으로 전달되지 않을 수 있습니다.

---

### **왜 Init 프로세스가 중요한가?**

컨테이너에서 Init 프로세스를 명확히 설정하지 않으면 다음과 같은 문제가 발생할 수 있습니다:

1. **좀비 프로세스 문제**
    - 자식 프로세스가 종료되었을 때 부모 프로세스가 이를 처리하지 않으면, 좀비 프로세스가 시스템에 남아 리소스를 낭비합니다.
2. **신호 전달 문제**
    - 컨테이너가 종료되거나 특정 이벤트가 발생할 때 신호가 애플리케이션에 전달되지 않아 비정상적으로 종료될 수 있습니다.
3. **리소스 누수**
    - 잘못된 프로세스 관리로 인해 컨테이너가 종료될 때 리소스가 해제되지 않을 위험이 있습니다.

---

### **Docker에서 Init 프로세스의 구현**

1. **기본 Init 프로세스**
    - 컨테이너 내부에서 기본적으로 실행되는 프로세스(`CMD` 또는 `ENTRYPOINT`)가 `PID 1`이 됩니다.
    - 예를 들어:
    
    이 경우 `bash`가 Init 프로세스로 동작합니다.
        
        ```
        FROM ubuntu
        CMD ["bash"]
        
        ```
        
2. **전용 Init 프로세스**
    - `tini`나 `dumb-init` 같은 Init 시스템을 사용하여 컨테이너의 프로세스를 더 효율적으로 관리할 수 있습니다.
    - `tini`는 작은 Init 시스템으로, 도커의 기본 Init 도구로 사용됩니다.
    - `tini` 사용 예:
        
        ```
        FROM ubuntu
        RUN apt-get update && apt-get install -y tini
        ENTRYPOINT ["/usr/bin/tini", "--"]
        CMD ["your-app"]
        
        ```
        
3. **도커의 `-init` 옵션**
    - Docker는 `-init` 옵션을 제공하여 기본 Init 시스템(`tini`)을 컨테이너에서 사용하도록 설정할 수 있습니다.
    - 예:
        
        ```bash
        docker run --init my-image
        
        ```
        

---

### **Init 프로세스의 필요성 예제**

### **1. Init 프로세스 없이 컨테이너 실행**

```bash
docker run ubuntu:latest bash -c "while true; do echo 'Running'; sleep 1; done"

```

- 컨테이너 내부에서 `bash`가 `PID 1`이 됩니다.
- 자식 프로세스를 적절히 관리하지 않으면 좀비 프로세스가 쌓일 수 있습니다.

### **2. Init 프로세스와 함께 실행**

```bash
docker run --init ubuntu:latest bash -c "while true; do echo 'Running'; sleep 1; done"

```

- `-init` 옵션을 사용하면 `tini`가 Init 프로세스로 동작하며 자식 프로세스와 신호를 적절히 관리합니다.

---

### **결론**

도커의 Init 프로세스는 컨테이너 내부의 프로세스 관리를 책임지는 첫 번째 프로세스입니다. 이를 올바르게 구성하면 컨테이너의 안정성과 효율성을 높일 수 있으며, `--init` 옵션이나 `tini` 같은 도구를 사용하면 더 안전하게 프로세스를 관리할 수 있습니다.

[도커의 이미지](https://www.notion.so/151478ab9488801e8c44cf72eeaac11e?pvs=21)