# setledsmac-backlight

Controle simples do backlight (via LED de Scroll Lock) para teclados no macOS.

> [!IMPORTANT]
> **Sobre a autoria e licença (GPL v2):**
> O código C central deste projeto (`source/SetLEDs/`) **NÃO** foi escrito por mim. Ele foi obtido diretamente do repositório público de **Damien Guard**:
> - **Repositório Original:** [https://github.com/damieng/setledsmac](https://github.com/damieng/setledsmac)
> - **Autor Original:** Damien Guard ([@damieng](https://github.com/damieng) / [https://damieng.com](https://damieng.com))
> - **Licença:** GNU General Public License Version 2 (GPLv2).
>
> **Por que posso publicar?**  
> A GPLv2 permite explicitamente que qualquer pessoa copie, modifique e redistribua o código (inclusive publicando um fork no GitHub), desde que:
> 1. Mantenha os avisos de copyright originais intactos (`Copyright 2015-2025 Damien Guard`).
> 2. Mantenha o arquivo [LICENSE](LICENSE) original da GPLv2.
> 3. Distribua o código-fonte sob os mesmos termos da GPLv2.

---

## O que este repositório adiciona

Em muitos teclados com iluminação embutida (como modelos que se identificam como "Usb KeyBoard"), a tecla Scroll Lock é usada para ligar ou desligar os LEDs de fundo. No macOS, não há suporte nativo por tecla para isso.

Este repositório adiciona uma camada de automação mínima e sem dependências:
- **`luz`**: Script wrapper em shell para ligar (`on`), desligar (`off`), alternar (`toggle`) ou ver o estado (`status`), filtrando automaticamente apenas pelo teclado USB (`-name "Usb*"`).
- **`com.local.setleds.backlight.plist`**: Arquivo de serviço `LaunchAgent` do macOS para manter a luz ligada automaticamente ao fazer login.
- **Compilação direta**: Instrução de compilação rápida via `clang` sem precisar do Xcode completo.

---

## Como compilar

Para compilar o binário sem o Xcode completo, basta usar o compilador nativo do sistema:

```bash
# 1. Cria a pasta ~/bin (se ainda não existir)
mkdir -p "$HOME/bin"

# 2. Compila direto para ~/bin/setleds
clang -O2 -Wall -Wextra \
  -framework IOKit \
  -framework CoreFoundation \
  source/SetLEDs/main.c \
  -o "$HOME/bin/setleds"
```

---

## Como usar o script `luz`

Copie o script para o seu `~/bin`:

```bash
cp luz "$HOME/bin/luz"
chmod +x "$HOME/bin/luz"
```

Comandos disponíveis:
```bash
luz on       # Liga o backlight (+scroll)
luz off      # Desliga o backlight (-scroll)
luz toggle   # Alterna o estado (^scroll) — comando padrão ao rodar só 'luz'
luz status   # Consulta o estado atual dos LEDs do teclado
```

---

## Ligar automaticamente no login (LaunchAgent)

Para que a luz do teclado acenda automaticamente sempre que você entrar na sua conta:

```bash
# 1. Copie o arquivo plist para os LaunchAgents do usuário
cp com.local.setleds.backlight.plist "$HOME/Library/LaunchAgents/"

# 2. Ative o serviço no macOS
launchctl bootstrap gui/$(id -u) "$HOME/Library/LaunchAgents/com.local.setleds.backlight.plist"
```

Se desejar desativar no futuro:
```bash
launchctl bootout gui/$(id -u) "$HOME/Library/LaunchAgents/com.local.setleds.backlight.plist"
```

---

## Atalho / Botão na Barra de Menus (Atalhos do macOS)

Se quiser um botão rápido na barra de menus para clicar e ligar/desligar:
1. Abra o app **Atalhos** (*Shortcuts*) no macOS.
2. Crie um novo atalho chamado **"Luz do Teclado"**.
3. Adicione a ação **"Executar Script Shell"** com o comando:
   ```sh
   $HOME/bin/luz toggle
   ```
4. Nos detalhes do atalho `(i)`, marque a opção **"Fixar na Barra de Menus"**.
