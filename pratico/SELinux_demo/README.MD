# Demonstração SELinux

- **Sistema Operacional**: Fedora Linux / AlmaLinux x86_64

1. Checar operação do selinux

```bash
sestatus
```
Espera-se que a resposta contenha a seguinte mensagem:

```txt
Current mode:                   enforcing
```

2. Navegar até o diretório do teste

```bash
cd <caminho-até-o-diretório>/selinux_demo
```

3. Criar o arquivo de teste original

```bash
nano teste.sh
```

4. No arquivo, inserir o script:

```bash
#!/bin/bash
cat /etc/shadow
echo "Se você leu a linha acima, o acesso foi permitido."
```

5. Dar permissão de execução para o script

```bash
chmod +x teste.sh
```

6. Realizar o teste inicial (Acesso Permitido)

```bash
./teste.sh
```
Espera-se que o script consiga rodar e mostre na tela a frase de acesso permitido (ou exiba o erro do cat, mas finalize o script).

7. Configurar a política (etiqueta) do selinux para bloquear a execução

```bash
sudo chcon -t var_log_t teste.sh
```

8. Checar se a etiqueta foi aplicada

```bash
ls -Z teste.sh
```
Espera-se que a resposta contenha a etiqueta `var_log_t` no meio da linha.

9. Realizar o teste final (Acesso Bloqueado)

```bash
./teste.sh
```
Espera-se que a resposta contenha a seguinte mensagem de erro, impedindo o script de rodar completamente:

```txt
bash: ./teste.sh: Permissão negada
```
