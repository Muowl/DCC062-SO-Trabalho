# Demonstração Seccomp

- **Sistema Operacional**: Ubuntu 24.04.4 LTS x86_64

1. Checar e instalar dependências do seccomp

```bash
sudo apt update && sudo apt install gcc libseccomp-dev -y
```

2. Navegar até o diretório do teste

```bash
cd <caminho-até-o-diretório>/seccomp_demo
```

3. Criar e abrir arquivo de configuração (código do filtro)

```bash
nano demo-seccomp.c
```

4. No arquivo, inserir:

```c
#include <stdio.h>
#include <seccomp.h>
#include <unistd.h>
#include <sys/stat.h>

int main() {
    printf("Iniciando programa...\n");

    // Permitir todas as chamadas por padrão
    scmp_filter_ctx ctx = seccomp_init(SCMP_ACT_ALLOW);

    // Bloquear a chamada 'mkdir'
    seccomp_rule_add(ctx, SCMP_ACT_KILL, SCMP_SYS(mkdir), 0);
    seccomp_load(ctx);

    printf("Tentando criar diretorio...\n");
    
    // Ação que ativa o bloqueio
    mkdir("pasta_proibida", 0777); 
    
    return 0;
}
```

5. Compilar o programa

```bash
gcc demo-seccomp.c -o demo-seccomp -lseccomp
```

6. Realizar os testes

- Execução (Criação de diretório)

demo-seccomp
```bash
./demo-seccomp
```
Espera-se que a resposta contenha a seguinte mensagem:

```txt
Bad system call (core dumped)
```
