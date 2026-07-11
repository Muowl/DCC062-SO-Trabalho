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

3. Criar arquivos de teste
```bash
echo "Este arquivo tem acesso bloqueado!" > blocked.txt
echo "Este arquivo tem acesso permitido!" > allowed.txt
```

4. Configurar as políticas (etiquetas) do selinux

```bash
sudo chcon -t shadow_t blocked.txt
sudo chcon -t user_home_t allowed.txt
```

5. Checar se as etiquetas foram aplicadas

```bash
ls -Z blocked.txt allowed.txt
```

6. Realizar os testes

- Leitura

blocked.txt
```bash
cat blocked.txt
```

allowed.txt
```bash
cat allowed.txt
```

- Escrita

blocked.txt
```bash
echo "teste" >> blocked.txt
```

allowed.txt
```bash
echo "teste" >> allowed.txt
```
