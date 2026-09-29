# ESP32 + MicroPython + LCD I2C 20x4 no Wokwi

Este projeto utiliza um **ESP32 com MicroPython** e um **display LCD 20x4 com interface I2C**, executado no **Wokwi Simulator dentro do VS Code**.

## Estrutura do projeto

```text
1EMA-CTFE/
├── diagram.json
├── firmware.bin
├── i2c_lcd.py
├── lcd_api.py
├── main.py
├── platformio.ini
└── wokwi.toml
```

## Requisitos

Antes de iniciar, tenha instalado:

- VS Code;
- extensão Wokwi Simulator;
- Python;
- `mpremote`.

A instalação do `mpremote` precisa ser feita apenas uma vez no computador:

```bash
python -m pip install mpremote
```

---

## 1. Clonar o repositório

Clone o repositório e abra a pasta do projeto no VS Code.

---

## 2. Iniciar a simulação

Inicie o **Wokwi Simulator** no VS Code.

Aguarde o MicroPython inicializar.

No terminal do Wokwi deverá aparecer:

```text
>>>
```

Isso indica que o interpretador MicroPython está pronto.

---

## 3. Enviar os arquivos Python para o ESP32 simulado

> **Importante:** no Wokwi para VS Code, os arquivos `.py` do computador não são automaticamente copiados para o sistema de arquivos interno do ESP32 simulado.

Com a simulação em execução, abra um **novo terminal do VS Code** e execute:

```bash
python -m mpremote connect port:rfc2217://localhost:4000 fs cp main.py :main.py
```

Depois:

```bash
python -m mpremote connect port:rfc2217://localhost:4000 fs cp i2c_lcd.py :i2c_lcd.py
```

E:

```bash
python -m mpremote connect port:rfc2217://localhost:4000 fs cp lcd_api.py :lcd_api.py
```

---

## 4. Conferir os arquivos enviados

Execute:

```bash
python -m mpremote connect port:rfc2217://localhost:4000 fs ls
```

O resultado deve apresentar arquivos semelhantes a:

```text
boot.py
main.py
i2c_lcd.py
lcd_api.py
```

Os tamanhos podem variar, mas nenhum dos três arquivos do projeto deve aparecer com `0 bytes`.

---

## 5. Executar o programa

Volte ao **terminal do Wokwi**, onde aparece:

```text
>>>
```

Pressione:

```text
Ctrl + D
```

O MicroPython fará um **soft reboot** e executará automaticamente o arquivo `main.py`.

O display LCD deverá apresentar as mensagens programadas.

---

## Conexões I2C

Neste projeto:

| LCD I2C | ESP32 |
|---|---|
| SDA | GPIO 21 |
| SCL | GPIO 22 |
| VCC | alimentação |
| GND | GND |

O endereço I2C utilizado no código é:

```python
0x27
```

A configuração do barramento no `main.py` é:

```python
i2c = I2C(
    0,
    sda=Pin(21),
    scl=Pin(22),
    freq=400000
)
```

---

## Atenção ao reiniciar o Wokwi

Ao **parar completamente e iniciar novamente** a simulação, os arquivos copiados para o sistema de arquivos interno do ESP32 podem desaparecer.

Se isso acontecer, execute novamente apenas os três comandos:

```bash
python -m mpremote connect port:rfc2217://localhost:4000 fs cp main.py :main.py
python -m mpremote connect port:rfc2217://localhost:4000 fs cp i2c_lcd.py :i2c_lcd.py
python -m mpremote connect port:rfc2217://localhost:4000 fs cp lcd_api.py :lcd_api.py
```

Depois volte ao terminal do Wokwi e pressione:

```text
Ctrl + D
```

Não é necessário reinstalar o `mpremote`.

---

## Problemas comuns

### `No module named mpremote`

O `mpremote` ainda não está instalado.

Execute:

```bash
python -m pip install mpremote
```

### Aparece somente `boot.py`

Os arquivos do projeto ainda não foram enviados ao ESP32 simulado.

Execute novamente os três comandos `fs cp`.

### Um arquivo aparece com `0 bytes`

Verifique se o arquivo possui conteúdo e se foi salvo no VS Code.

Depois envie-o novamente.

Exemplo:

```bash
python -m mpremote connect port:rfc2217://localhost:4000 fs cp i2c_lcd.py :i2c_lcd.py
```

### `could not enter raw repl`

Pare e inicie novamente o Wokwi.

Aguarde aparecer:

```text
>>>
```

e tente o comando novamente.

### O LCD não apresenta nenhuma mensagem

Primeiro confirme se os arquivos estão no ESP32:

```bash
python -m mpremote connect port:rfc2217://localhost:4000 fs ls
```

Depois confirme se o programa foi iniciado com:

```text
Ctrl + D
```

Também verifique as conexões:

- SDA → GPIO 21;
- SCL → GPIO 22;
- endereço I2C → `0x27`.

---

## Fluxo resumido

```text
Clonar o repositório
        ↓
Abrir no VS Code
        ↓
Iniciar o Wokwi
        ↓
Aguardar >>>
        ↓
Copiar main.py
Copiar i2c_lcd.py
Copiar lcd_api.py
        ↓
Pressionar Ctrl + D
        ↓
Executar o projeto
```
