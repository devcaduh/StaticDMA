<div align="center">

# STATIC DMA

**Painel DMA DirectX 11 / ImGui com Auto-Updater nativo, gerenciador de configs nomeadas e detecção automática de versões do FiveM.**

![Platform](https://img.shields.io/badge/Platform-Windows%20x64-E63946?style=for-the-badge)
![Theme](https://img.shields.io/badge/Theme-Crimson%20Red-E63946?style=for-the-badge)
![Loader](https://img.shields.io/badge/Binary-StaticLoader.exe-181B22?style=for-the-badge)
![Auto--Updater](https://img.shields.io/badge/Auto--Updater-Active-E63946?style=for-the-badge)

</div>

---

## Principais Recursos

- **Identidade Visual STATIC DMA (Tema Vermelho `#E63946`)**: interface limpa em vermelho carmesim, ícones vetoriais redesenhados com proporção unificada (incluindo emblema `STATIC` e engrenagem na aba `Configs`) e notificações padronizadas em vermelho.
- **Abertura Instantânea (~15–50 ms)**: carregamento assíncrono do DMA/FiveM e checagem do Auto-Updater em segundo plano — o painel abre imediatamente sem travar a tela.
- **Auto-Updater Nativo Integrado**: verifica novas versões automaticamente ao abrir, exibe tela dedicada com barra de progresso vermelha, porcentagem e tamanho em MB, valida integridade via SHA-1, migra automaticamente para `StaticLoader.exe` e reinicia exibindo a notificação `"Atualizado com sucesso!"`.
- **Gerenciador de Configs Nomeadas (`Saved Configs`)**: crie, salve, carregue (`▶`) e exclua perfis de configuração diretamente no painel.
- **Detecção Automática de Versões do FiveM**: identifica automaticamente novos builds (`FiveM_bXXXX_GTAProcess.exe`), registra no cache local e resolve a estrutura do jogo dinamicamente sem exigir atualização manual da lista de processos.

---

## Histórico de Atualizações (Changelog)

### `25/11/2026` — Botão `Reload Static` & Changelog Público
- **Novo botão `Reload Static` (âmbar/amarelo)**: adicionado na seção `Configs → Session` logo acima de `Unload Static`, com ícone vetorial de recarga. Ao clicar, executa o desligamento limpo completo (salva configs, encerra WebSocket e threads, reverte patches ativos, libera DirectX 11/ImGui e fecha a sessão DMA via `VMMDLL_Close`), reinicia automaticamente o `StaticLoader.exe` recarregando todos os módulos do zero e exibe a notificação `"Static recarregado com sucesso!"`.
- **Changelog público (`devcaduh/StaticDMA`)**: sincronização automática deste `README.md` com o repositório público a cada nova atualização.

### `25/11/2026` — Static DMA & `StaticLoader.exe`
- **Renomeação no Auto-Update**: ao atualizar, o aplicativo migra automaticamente de `free-river.exe` para **`StaticLoader.exe`**, removendo o arquivo legado `.old` na reinicialização.
- **Rodapé atualizado**: alterado o texto do rodapé de `Static v2.0` para **`Static DMA`** (tanto no painel principal quanto na tela de atualização).
- **Documentação**: adicionado este `README.md` com o histórico completo de modificações por data.

### `25/11/2026` — Auto-Updater & Notificações em Vermelho
- **Barra de progresso do Auto-Updater**: borda do cartão, trilha e preenchimento atualizados para vermelho carmesim (`#E63946`).
- **Notificações (`Notify`)**: todas as notificações do sistema (auto-update, configs e alertas internos do painel) padronizadas em vermelho (`#E63946`).
- **Publicação automática**: integração direta com `git push` para entrega imediata no Auto-Updater.

### `25/11/2026` — Limpeza de Módulos & Auto-Detecção de Versões do FiveM
- **Auto-detecção de builds do FiveM**: novo sistema via enumeração de processos DMA (`VMMDLL_ProcessGetInformationAll`) que detecta qualquer build `FiveM_bXXXX_GTAProcess.exe` ainda não listado, salva em `%APPDATA%\River\auto_builds.cfg`, aplica o perfil estrutural correspondente e exibe notificação da versão detectada.
- **Remoção completa de módulos**:
  - Removido **`Fake FPS`** (cartão em `Misc → Exploits` e rotinas de backend).
  - Removido tudo relacionado a **Veículos** (`Vehicle ESP`, aba `Vehicles`, `Car colours`, seção `Misc → Vehicle`, `Unlock`, `Repair`, `Plate changer` e banner de placa).
  - Removido **`Armed Behind You`** (cartão em `Visuals → Health bar`, cor `Warning` em `Player colours` e rotinas de monitoramento).

### `25/11/2026` — Ícone de Engrenagem na Aba Configs
- Substituído o ícone da aba **Configs** na barra superior por uma **engrenagem vetorial** de 8 dentes simétricos nos estados ativo e inativo.

### `25/11/2026` — Inicialização Instantânea & Redesign de `Saved Configs`
- **Abertura instantânea do painel**: eliminado atraso fixo na limpeza de `.old`, movida a checagem de update e a inicialização de DMA/FiveM para threads em segundo plano e reduzido o intervalo de reconexão de `3,0s` para `0,5s` (abertura em `~11–50 ms`).
- **Nova box `Saved Configs`**: removida por completo a antiga box `Config`; movidos `Name Config` e `Save Config` para o topo de `Saved Configs` e adicionado o **botão vermelho com ícone de lixeira** ao lado do `▶` (Play) para excluir configurações salvas.

### `25/11/2026` — Redesign Completo STATIC (Tema Vermelho, Ícones e Crosshair)
- **Tema Vermelho (`#E63946`)**: substituição completa da paleta azul em todas as abas, toggles, checkboxes, sliders, botões, seletores e indicador de FPS (`0` pixels azuis residuais).
- **Rebranding `RIVER` → `STATIC`**: atualizados cabeçalho, emblema principal, janela, rodapé e textos da aba `Session` (`Unload Static`).
- **Ícones Vetoriais Redesenhados**: todos os 15 ícones de abas e cartões e o emblema do topo refeitos com proporção e traço uniformes.
- **Remoção da Crosshair Roxa**: removida a mira fixa roxa central em `RenderFrame`.

### `24/11/2026` — Sistema de Configs Nomeadas
- Adicionados os controles `Name Config`, `Save Config` e a lista `Saved Configs` com botão vetorial `▶` (Play) para carregar perfis salvos em `%APPDATA%\River\configs\`.

### `24/11/2026` — Limpeza da Aba Configs
- Removida a box `Shortcuts` e o botão `Reset to defaults` da categoria `Configs → General`.

### `24/11/2026` — Auto-Updater Nativo Embarcado
- Implementado sistema de auto-atualização em C++20 nativo embarcado diretamente no binário (seções `.updt` / `.updd`, 100% compatível com ASLR), com tela dedicada de atualização, verificação SHA-1, substituição atômica sem `.bat` e notificação pós-atualização `"Atualizado com sucesso!"`.
