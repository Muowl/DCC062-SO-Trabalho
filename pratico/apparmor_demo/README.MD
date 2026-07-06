# Demonstração Apparmor

- **Sistema Operacional**: Ubuntu 24.04.4 LTS x86_64

1. Checar operação do apparmor

```bash
aa-status
```
Espera-se que a resposta contenha a seguinte mensagem:

```txt
apparmor module is loaded.
```

2. Navegar até o diretório do teste

```bash
cd <caminho-até-o-diretório>/apparmor_demo
```

3. Criar arquivos de teste
```bash
echo "Este arquivo tem acesso bloqueado!" > blocked.txt
echo "Este arquivo tem permissão apenas para leitura!" > readonly.txt
echo "Este arquivo tem permissão para leitura e escrita!" > readwrite.txt
```

3. Criar e abrir arquivo de configuração do apparmor

```bash
sudo nano /etc/apparmor.d/demo-so
```

4. No arquivo, inserir:

```apparmor
#include <tunables/global>

profile demo-so {
  #include <abstractions/base>

  # Permitir bash
  /usr/bin/bash rix,
  /bin/bash rix,

  # Permitir cat
  /usr/bin/cat rix,
  /bin/cat rix,

  # Permitir echo, útil para teste de escrita
  /usr/bin/echo rix,
  /bin/echo rix,

  # Arquivo bloqueado
  deny <caminho>/apparmor_demo/blocked.txt rw,

  # Arquivo somente leitura
  <caminho>/apparmor_demo/readonly.txt r,

  # Arquivo leitura e escrita
  <caminho>/apparmor_demo/readwrite.txt rw,
}
```

5. Carregar o perfil

```bash
sudo apparmor_parser -r /etc/apparmor.d/demo-so
```

6. Realizar os testes

- Leitura

blocked.txt
```bash
aa-exec -p demo-so -- bash --noprofile --norc -c 'cat blocked.txt'
```

readonly.txt
```bash
aa-exec -p demo-so -- bash --noprofile --norc -c 'cat readonly.txt'
```

readwrite.txt
```bash
aa-exec -p demo-so -- bash --noprofile --norc -c 'cat readwrite.txt'
```

- Escrita

blocked.txt
```bash
aa-exec -p demo-so -- bash --noprofile --norc -c 'echo "teste" >> blocked.txt'
```

readonly.txt
```bash
aa-exec -p demo-so -- bash --noprofile --norc -c 'echo "teste" >> readonly.txt'
```

readwrite.txt
```bash
aa-exec -p demo-so -- bash --noprofile --norc -c 'echo "teste" >> readwrite.txt'
```

