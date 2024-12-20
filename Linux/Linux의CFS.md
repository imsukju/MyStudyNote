리눅스의 **CFS(Completely Fair Scheduler)**는 리눅스 커널에서 사용되는 스케줄러 중 하나로, 프로세스가 CPU 시간을 최대한 공평하게 분배받을 수 있도록 설계되었습니다. CFS는 리눅스 커널 2.6.23 버전부터 기본 스케줄러로 도입되었으며, 이전에 사용되던 O(1) 스케줄러를 대체했습니다. CFS는 선점형(Preemptive) 스케줄러이며, 공정성을 핵심으로 합니다.

---

### **CFS의 주요 특징**

1. **공정성(Fairness):**
    - 각 프로세스가 CPU를 사용할 수 있는 시간을 가능한 한 균등하게 보장합니다.
    - "완벽한 공평함"은 각 프로세스가 동일한 우선순위라면 동일한 실행 시간을 가져야 한다는 것을 의미합니다.
2. **가상 런타임(Virtual Runtime, vruntime):**
    - 각 프로세스는 `vruntime`이라는 값을 가집니다.
    - `vruntime`은 프로세스가 CPU를 사용한 시간을 추적하며, CPU 사용 시간이 많을수록 값이 커집니다.
    - CFS는 `vruntime` 값이 가장 작은 프로세스(즉, CPU를 가장 적게 사용한 프로세스)를 우선적으로 실행합니다.
3. **RB-Tree 기반 구현:**
    - CFS는 Red-Black Tree(RB-Tree)라는 자료구조를 사용하여 프로세스를 관리합니다.
    - RB-Tree는 균형 이진 탐색 트리로, 삽입과 삭제가 O(log n)의 시간 복잡도를 가집니다.
    - `vruntime` 값이 작은 순서대로 정렬되며, 최상위 노드에 있는 프로세스가 CPU를 먼저 사용합니다.
4. **선점형 스케줄링:**
    - 실행 중인 프로세스가 다른 프로세스보다 `vruntime`이 훨씬 커지면, 스케줄러는 현재 프로세스를 선점하고 새로운 프로세스를 실행합니다.
5. **우선순위와 Nice 값:**
    - 프로세스의 우선순위와 `nice` 값에 따라 `vruntime` 증가 속도가 조정됩니다.
    - `nice` 값이 낮을수록 우선순위가 높아지며, `vruntime`이 느리게 증가합니다.

[상세히](https://www.notion.so/153478ab94888082a5e5c538b7ea12f4?pvs=21)

---

### **작동 원리**

1. **`vruntime` 계산:**
    - CPU를 사용할 때마다 프로세스의 `vruntime`이 증가합니다.
    - 증가량은 프로세스의 우선순위와 실행 시간에 따라 다릅니다.
2. **프로세스 선택:**
    - RB-Tree의 최상위 노드(가장 작은 `vruntime` 값을 가진 프로세스)를 선택하여 실행합니다.
3. **컨텍스트 스위칭:**
    - 실행 중인 프로세스의 `vruntime`이 다른 프로세스보다 훨씬 커지면, 스케줄러는 프로세스를 선점합니다.

---

### **장점**

- 프로세스의 실행 순서를 `vruntime` 기준으로 정렬하여 공정성을 보장합니다.
- RB-Tree를 사용해 높은 효율성을 유지합니다.
- 우선순위와 `nice` 값을 통해 다양한 워크로드를 효과적으로 처리할 수 있습니다.

### **단점**

- 공평성을 추구하므로 특정 시나리오에서는 실시간 성능이 다소 떨어질 수 있습니다.
- 고정된 타임 슬라이스를 사용하지 않아 예상 실행 시간이 명확하지 않을 수 있습니다.

---

### **CFS 관련 주요 파일**

CFS는 `sysfs` 또는 `proc`를 통해 설정 및 상태를 확인할 수 있습니다:

- `/proc/sys/kernel/sched_latency_ns`: 타임 슬라이스의 최대 대기 시간
- `/proc/sys/kernel/sched_min_granularity_ns`: 최소 타임 슬라이스 크기

---

추가적으로 알고 싶은 부분이 있다면 질문해 주세요! 😊