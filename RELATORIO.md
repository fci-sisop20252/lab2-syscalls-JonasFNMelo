# 📝 Relatório do Laboratório 2 - Chamadas de Sistema

---

## 1️⃣ Exercício 1a - Observação printf() vs 1b - write()

### 💻 Comandos executados:
```bash
strace -e write ./ex1a_printf
strace -e write ./ex1b_write
```

### 🔍 Análise

**1. Quantas syscalls write() cada programa gerou?**
- ex1a_printf: 9 syscalls
- ex1b_write: 7 syscalls

**2. Por que há diferença entre os dois métodos? Consulte o docs/printf_vs_write.md**

```
A diferenca é encontrada por conta que o printf() junta as mensagens dentro de um buffer, e a quantidade de syscalls é variavel pois depende do tamanho desse buffer, enquanto isso o metodo write(), em cada vez que ele é chamado uma syscall é realizada, idependente do buffer
```

**3. Qual método é mais previsível? Por quê você acha isso?**

```
O metodo write é mais previsível, pois as quantidades de chamadas é exatamente igual a quantidade de métodos write() chamados no codigo.
```

---

## 2️⃣ Exercício 2 - Leitura de Arquivo

### 📊 Resultados da execução:
- File descriptor: 3
- Bytes lidos: 127

### 🔧 Comando strace:
```bash
strace -e openat,read,close ./ex2_leitura
```

### 🔍 Análise

**1. Qual file descriptor foi usado? Por que não começou em 0, 1 ou 2?**

```
o fd usado foi o 3, pois o 0, 1 e 2 são usador pelo stdin, stdout e stderr, assim sendo o 3 o primeiro fd disponivel para o uso 
```

**2. Como você sabe que o arquivo foi lido completamente?**

```
É possivel saber que o arquivo foi completamente lido através do numero de bytes lidos em comparação com o tamanho do arquivo.
```

**3. Por que verificar retorno de cada syscall?**

```
A verificação é importante, pois, existe a possibilidade de falha a cada syscall, e falhas devem ser tratadas 
```

---

## 3️⃣ Exercício 3 - Contador com Loop

### 📋 Resultados (BUFFER_SIZE = 64):
- Linhas: 25 (esperado: 25)
- Caracteres: 1300
- Chamadas read(): 21
- Tempo: 0.000150 segundos

### 🧪 Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |    82             |    0.000196       |
| 64          |    21             |     0.000150      |
| 256         |     6            |     0.000064      |
| 1024        |       2          |       0.000063    |

### 🔍 Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
O tamanho do buffer afeta no número de syscalls, pois quanto maior o buffer, mais informações ele aguenta, ou seja mais informações são passadas em cada chamada de sistema
```

**2. Todas as chamadas read() retornaram BUFFER_SIZE bytes? Discorra brevemente sobre**

```
Não, pois sempre na ultima chamada do read(), sera chamado apenas o que faltou do arquivo para ser lido, ou seja o “bytesDoArquivo”% “bytesDoBuffer"
```

**3. Qual é a relação entre syscalls e performance?**

```
A relação é, quanto menos syscalls, mais perfomace um programa vai ter, pois tera que fazer menos transições entre o modo Kernel e o modo user
```

---

## 4️⃣ Exercício 4 - Cópia de Arquivo

### 📈 Resultados:
- Bytes copiados: 1364
- Operações: 6
- Tempo: 0.000259 segundos
- Throughput: 5192.48 KB/s

### ✅ Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [X] Idênticos [ ] Diferentes

### 🔍 Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
Porque write() pode escrever menos bytes que o lido.Ao conferir a gente garante que todos os dados foram gravados, evitando perda ou corrupção
```

**2. Que flags são essenciais no open() do destino?**

```
O_WRONLY | O_CREAT | O_TRUNC são essenciais para abrir o arquivo para escrita e criar caso nao haja e sobrescrever se já existir
```

**3. O número de reads e writes é igual? Por quê?**

```
Sim, pois cada read() do arquivo origem possui um write() correspondente no destino
```

**4. Como você saberia se o disco ficou cheio?**

```
Caso o disco fique cheio, write() retorna -1 e o programa indica um erro 
```

**5. O que acontece se esquecer de fechar os arquivos?**

```
O programa pode atingir o limite de arquivos abertos e falhar ao abrir novos.
```

---

## 🎯 Análise Geral

### 📖 Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
Quando o programa usa open(), read() ou write() ele não acessa o hardware diretamente, mas pede ao kernel que faça essa operação.
```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
File descriptors são números que o sistema usa para lembrar quais arquivos cada programa está usando, e tambem permite leitura e escrita de arquivos
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
O tamanho do buffer afeta a performance porque buffers maiores fazem menos chamadas ao sistema. Buffers pequenos geram muitas chamadas, tornando o programa mais lento.
```

### ⚡ Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** O mais rápido foi time ./ex4_copia

**Por que você acha que foi mais rápido?**

```
O programa foi mais rápido porque é simples e faz só uma coisa, copiar o arquivo direto com syscalls, sem overhead de funções extras.
```

---

## 📤 Entrega
Certifique-se de ter:
- [X] Todos os códigos com TODOs completados
- [X] Traces salvos em `traces/`
- [X] Este relatório preenchido como `RELATORIO.md`

```bash
strace -e write -o traces/ex1a_trace.txt ./ex1a_printf
strace -e write -o traces/ex1b_trace.txt ./ex1b_write
strace -o traces/ex2_trace.txt ./ex2_leitura
strace -c -o traces/ex3_stats.txt ./ex3_contador
strace -o traces/ex4_trace.txt ./ex4_copia
```
# Bom trabalho!
