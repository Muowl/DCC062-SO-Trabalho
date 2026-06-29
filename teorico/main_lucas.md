# Tópicos Lucas Vieira Resende

## Tópico 5: Atendimento dos requisitos pelos Sistemas Operacionais

### Android

#### Responsividade

A responsividade do Android depende de um caminho bem definido entre o input do hardware, a thread principal da aplicação e o display. Ao iniciar um app, o Andriod cria um processo com uma thread principal, com os componentes-padrão do app rodando nessa mesma thread. Essa thread é responsável pelos eventos da interface gráfica, de forma que seu bloqueio impacta a responsividade. O loop da thread é implementada com Looper (loop infinito da aplicação), MessageQueue (fila de tarefas) e Handler (interface para comunicação entre input do usuário e a thread).[[1](https://developer.android.com/guide/components/processes-and-threads)]

O input é normalizado antes de chegar ao app, com os drivers do disositivo traduzindo sinais de touchscreens, teclados, botões e dispositivos de entrada e saída em eventos do Linux. Posteriormente, esses eventos são traduzidos em eventos do Android pelo InputReader e então encaminhados para a janela apropriada pelo InputDispatcher. [[3](https://source.android.com/docs/core/interaction/input)]

A sincronização dos quadros é gerenciada pelo Choreographer, que coordena o timing das animações e inputs. Na camada de display, a renderização é sincronizada pelo VSync, com utilização do SurfaceFlinger para composição e transferência do trabalho para o hardware com Hardware Composer, visando a garantir responsividade nos gráficos. [[3](https://developer.android.com/reference/android/view/Choreographer)]

A não responsividade de aplicações é detectada por meio de timeouts de App Not Responding, com um valor default de 5 segundos. A responsividade de serviços no foreground e no background também é monitorada com períodos de timeout de 20 segundos e 200 segundos, respectivamente. [[4](https://developer.android.com/topic/performance/anrs/diagnose-and-fix-anrs)]

Para comunicações inter-processos, é utilizado o Binder IPC, que abstrai a chamada de métodos em processos remotos em uma interface, sendo usado tanto para comunicação app-sistema e app-app. [[5](https://source.android.com/docs/core/architecture/ipc/binder-overview)]

O Android Zygote é um processo de sistema que é iniciado no boot é serve como template de software para todas as aplicações no sistema operacional. Quando um app necessita de um processo, o zZyfote é o responsável para fazer o fork e a configuração do processo. Esse modelo permite a redução da latência de startup das aplicações. [[6](https://source.android.com/docs/core/runtime/zygote)]

#### Eficiência energética

O Android conserva energia evitando processamento em períodos ociosos, limitando a execução de tarefas no background, suspendendo o sistema quando possível e com sinais de thermal throttling. O Doze, funcionalidade de gerenciamento de bateria no sistema operacional, deliberadamente atrasa operações de CPU quando o dispositivo está inativo por um longo período, periodicamente abrindo janelas para manitenção de atividades pendentes. [[7](https://source.android.com/docs/core/power/platform_mgmt)]

O SystemSuspend é responsável por manter um timer de suspensão e altera o estado do dispositivo para suspender caso não haja wakelocks, os quais previnem que o dispositivo seja suspenso. [[8](https://source.android.com/docs/core/power/systemsuspend)]

Há ainda recursos como o App Standby Buckets que classifica as aplicações por interação recente para gerenciar restrições de recursos [[9](https://developer.android.com/topic/performance/appstandby)], o App Hibernation, que trata das permissões, arquivos e cache de apps não muito usados [[10](https://source.android.com/docs/core/perf/hiber)], e o PowerManagement, que permite que apps acessem informações sobre mudanças no estado térmico do dispositivo [[11](https://source.android.com/docs/core/power/thermal-mitigation)].

#### Operação sob restrição de memória

No que diz respeito ao gerenciamento de memória, o Android age como um sistema operacional padrão, incluindo compressão, priorização de processos e encerramento de processos de menor importância, dependendo para isso do kswapd, o swap daemon do kernel do Linux [[12](https://developer.android.com/topic/performance/memory-management)]. Quando iss é insuficiente, é usado o low-memory killer, que também auxilia no encerramento de processos [[13](https://developer.android.com/topic/performance/memory-management)]. O Android moderno conta ainda com um daemon de enceramento de processos no espaço de usuário, o lmkd [[14](https://source.android.com/docs/core/perf/lmkd)].

#### Segurança e isolamento de aplicativos

No quesito segurança, o Android combina o isolamento do kernel, permissões, SELinux, verified boot, app signing e IPC controlado. Cada app possui seu próprio processo no kernel e com seu próprio UID, utilizando os recursos mencionados para isolar apps do sistema e de outros apps. [[15](https://source.android.com/docs/security/app-sandbox), [16](https://source.android.com/docs/security/features/selinux)].

A integridade do sistema é verificada no boot, quando o Android Verified Boot checa o sistema e inclui proteções para que agentes maliciosos não consigam explorar facilmente o dispositivo, mesmo em versões mais atingas. [[17](https://source.android.com/docs/security/features/verifiedboot/avb)]

#### Portabilidade e compatibilidade de hardware

A arquitetura do Android separa o sistema operacional de serviços de hardwares específico por meio de HALs -- interfaces padrão implementadas por fabricantes --, o que permite a abstração da integração com drivers em baixo nível. [[18](https://source.android.com/docs/core/architecture)]

A portabilidade do kernel é mantida pelo Generic Kernel Image project, o qual expõe versões estáveis das interfaces para que o kernel e os módulos dos fabricantes possam ser atualizados com maior liberdade e independência. [[19](https://source.android.com/docs/core/architecture/kernel/generic-kernel-image)]

### Fuchsia

#### Segurança

Zircon, o kernel do Fuchsia, é baseado no paradigma da orientação a objetos e encapsulamento, de forma que processos são isolados por padrão e os recursos são acessados por handles, uma referência local de um processo a um objeto do kernel. Cada handle possui direitos predefinidos que dizem respeito as operações que o processo pode realizar. [[20](https://fuchsia.dev/fuchsia-src/concepts/principles/secure), [21](https://fuchsia.dev/fuchsia-src/get-started/learn/intro/zircon)].

Um novo processo criado começa sem capacidades próprias, podendo utilizar apenas os recursos passados pelo criador por meio de handles. Os mecanismos de comunicação inter-processo do sistema operacional permitem a transferência tanto de recursos quanto de handles, sendo que quando um handle é enviado por um canal de comunicação, sua propriedade é transferida do processo remetente ao processo destino, em um modelo de ownership que auxilia na segurança do sistema. [[22](https://fuchsia.dev/fuchsia-src/get-started/learn/intro/sandboxing), [23](https://fuchsia.dev/fuchsia-src/get-started/learn/intro/zircon)]

#### Atualizabilidade

No Fuchsia, com sua filosofia de microkernel, o software é implementado não como uma aplicação monolítica, mas como pacotes que podem ser atualizados de forma independente e enviados sob demanda. Isso facilita imensamente a atualizabilidade (updatability) do sistema, pois pacotes e alterações podem enviados para dispositivos sem necessidade de grande esforço de coordenação para a implementação em modelos individuais [[24](https://fuchsia.dev/fuchsia-src/get-started/learn/intro/packages)].

O versionamento da API do Fuchsia é projetado para que a plataforma possa evoluir sem quebrar a compatibilidade de componentes antigos. O Component Manager do SO não inicializa componentes cuja compatibilidade com o restante do sistema não tenha garantia documentada de compatibilidade com os componentes do produto. [[25](https://fuchsia.dev/fuchsia-src/concepts/versioning/compatibility)]

#### Modularidade

O Fuchsia é altamente modular, já que as unidades principais de software são componentes que são executados em seu próprio ambiente e comunicam com outros componentes através de "capabilities". Isso permite que o software de produtos ou dispositivos individuais possua combinações diferentes de componentes, de forma a permitir que produtos diferentes possuam as capacidades necessárias para seu propósito. [[26](https://fuchsia.dev/fuchsia-src/get-started/learn/components)]

Quanto à organização dos componentes, cada componente é identificado por um URL e documentado em um "component manifest", o qual descreve as capacidades e forma de execução [[27](https://fuchsia.dev/fuchsia-src/concepts/components/v2/introduction)]. Componentes são organizados topologicamente, de forma que componentes pais oferecem capabilities aos componentes filhos de forma explicita [[28](https://fuchsia.dev/fuchsia-src/concepts/components/v2/capabilities)].

#### Escalabilidade entre classes de dispositivos

As configurações de build do Fuchsia são definidas de acordo com o produto. Nela, são documentadas a arquitetura, o hardware, os drivers e outros parâmetros, o que guia a escolha e design de componentes para cada classe de dispositivo de acordo com as funcionalidades requeridas. [[29](https://fuchsia.dev/fuchsia-src/get-started/build_fuchsia)]

#### Compatibilidade com o ecossistema Linux

Para compatibilidade com programas desenvolvidos para Linux, o Fuchsia possui um runner chamado Starnix capaz criar um ambiente de execução Linux em espaço de usuário. No entanto ainda que essa estratégia permita que algumas aplicações Linux sejam executadas em sistemas Fuchsia sem modificações, ela não torna esses sistemas 100% compatíveis com o Linux. Nesse sentido, o Starnix é projetado de forma a realizar testes para verificar a compatibilidade de aplicações individuais. [[30](https://fuchsia.dev/fuchsia-src/concepts/components/v2/starnix)]

## Tópico 8: Exclusão mútua, sincronização e IPC



## Tópico 9: Gerenciamento de Processos e escalonadores
