<div align="center">

# STATIC DMA

**Cheat de DMA para FiveM focado para PVP Competitivo.**

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

### `25/11/2026` — Remoção Completa de `SET_ENTITY_HEALTH` (`[ElectronBypass]`) & Layout Balanceado em `Misc → Exploits`
- **Remoção completa de `SET_ENTITY_HEALTH` / `[ElectronBypass]` (UI + Backend + Shutdown)**:
  - Removido o cartão `SET_ENTITY_HEALTH` da aba `Misc → Exploits` e reconstruída a página (`0x091A70`) em 2 colunas balanceadas (**`Anti-ESP Bypass`** à esquerda e **`Render / VSync`** à direita, sem coluna vazia).
  - Neutralizadas com retorno imediato (`xor eax, eax; ret`) todas as rotinas de backend do módulo (`0x03C6B0` aplicação, `0x03C8D0` reversão e `0x03C090` logger de status), bloqueando inclusive ativações via WebSocket ou configs antigos.
  - Flags globais `0x1B115A` e `0x1B115B` forçadas permanentemente para `0` em `enforce_disabled_features()` e removida a rotina de reversão correspondente do pipeline de encerramento.

### `25/11/2026` — Conexão DMA/Build Ultra-Rápida & Box `Makcu` em Configs
- **Aceleração completa de conexão DMA e detecção de build (`b3889`, etc.) — Back-End e Front-End**:
  - **Busca em 1 tentativa**: `TryConnectDMA` agora tenta primeiro o último build salvo em `auto_builds.cfg` (ou `b3889`) e, caso diferente, enumera todos os processos em RAM numa única chamada (`VMMDLL_ProcessGetInformationAll`), eliminando as 8–11 sondagens sequenciais antigas.
  - **Scanner em passagem única (15×–25× menos tráfego DMA)**: substituiu as 8–11 varreduras separadas de `64 MB` em `ResolveOffsets` (que incluíam 3 varreduras mortas de veículos e até 4 varreduras redundantes de data de build, somando `512–704 MB` por abertura) por uma única varredura com 1 handle scatter reutilizável que resolve `World`, `Replay`, `Viewport`, `Camera` e `LocalPlayer` de uma só vez com parada antecipada.
  - **Inicialização em paralelo**: `Makcu` (`0x01F580`) e `DMA Keyboard` (`0x001CD0`, que podia levar 5–20s em `csrss.exe`/PDB) agora rodam em threads assíncronas dedicadas sem travar o status.
  - **Front-End instantâneo**: o painel marca `DMA: connected` e exibe o build (`b3889`) imediatamente após o scan do FiveM (sem esperar Makcu/Keyboard/WebSocket) e mostra `connecting...` no cartão `Status` enquanto inicializa.
- **Nova Box `Makcu` em `Configs → General`**:
  - Cartão dedicado com ícone vetorial de mouse, status ao vivo (`connected` / `connecting...` / `not connected`), porta COM e versão de firmware (`Device`), botão **`Connect Makcu` / `Disconnect Makcu`** e auto-conexão assíncrona na abertura usando o driver serial nativo CH343/CH340 (4 Mbaud, `km.buttons(1)` + `km.version()`), com desconexão limpa da porta COM no `Unload` / `Reload`.

### `25/11/2026` — Melhorado o Sistema de Limpar a memória do jogo, Correção do Auto-Updater & Ajuste do `Reload Static`
- **Encerramento completo reescrito em 4 etapas seguras (`0x081D0D` e `0x0A84C0`)**:
  1. **Sinalização paralela & WebSocket (`0x081D0D` / `0x09EB60`)**: ativa atomicamente `0x1B1172 = 1`, cancela imediatamente o Auto-Updater (fechando a sessão WinHTTP ativa), oculta a janela do overlay no instante do clique (`SW_HIDE`), sinaliza todas as flags de worker e condition variables de uma vez, envia close frame RFC 6455 (`1000`), chama `shutdown(SD_BOTH)` + `closesocket` em clientes e listener e aguarda a thread do WebSocket com timeout limitado (`1,5s`), sem travamento ou `__debugbreak()`.
  2. **Threads em segundo plano (`0x081D6A..0x081EA7` / `0x00F680`)**: aguarda de forma independente e com timeout seguro as threads assíncronas de inicialização DMA/FiveM, Auto-Updater, DMA Keyboard e todas as 4 threads do subsistema ESP (corrigindo o retorno prematuro de `0x00F680`), encerrando e zerando ambos os handles de scatter DMA (`0x1B13F0` e `0x1B16C0`).
  3. **Reversão blindada de patches em memória (`0x03C8D0` / `0x03D1E0`)**: valida handle DMA e PID antes de reverter `SET_ENTITY_HEALTH` e `Anti-ESP Bypass`, executa até 3 tentativas em caso de falha transitória de barramento e limpa consistentemente todas as flags de estado.
  4. **DirectX 11, ImGui e liberação atômica da placa DMA (`0x080740` / `0x0A84C0`)**: corrigida a ordem de liberação (ImGui primeiro com validação de ponteiros nulos e eliminação de double-destroy no renderer auxiliar → `ClearState()` + `Flush()` + `Release()` na ordem reversa `RTV → SwapChain → DeviceContext → Device` → `DestroyWindow`/`UnregisterClassA` → `VMMDLL_Close` atômico e idempotente que nunca fecha handle nulo nem fecha duas vezes no `atexit`).
- **Correção definitiva do congelamento em 96% (`StaticLoader.exe.old` não respondendo)**:
  - Corrigida falha de acesso (`0xC0000005` em `0x03712C`) no `atexit` legado de veículos (`0x001830` / `0x0A8F10`), que travava o processo ao encerrar ou reiniciar pelo Auto-Updater.
  - O Auto-Updater agora respeita imediatamente o clique em `Unload Static`, avança até `100%`, executa o encerramento limpo completo antes de reiniciar para `StaticLoader.exe` com `--cleanup-pid` e remove automaticamente quaisquer sobras `.old`/`.new` em segundo plano (além de removido `StaticLoader.exe.bak` da pasta principal).
- **Ajuste visual em `Configs → Session`**: botão amarelo **`Reload Static`** redimensionado para o tamanho exato de `Unload Static` (`28px` de altura, mesma fonte e proporção), removido o ícone interno e adicionada a descrição dedicada `"Restarts Static and reloads all modules and DMA."` logo acima do botão.

### `25/11/2026` — Botão `Reload Static` & Changelog Público
- **Novo botão `Reload Static` (âmbar/amarelo)**: adicionado na seção `Configs → Session` logo acima de `Unload Static`, com ícone vetorial de recarga. Ao clicar, executa o desligamento limpo completo (salva configs, encerra WebSocket e threads, reverte patches ativos, libera DirectX 11/ImGui e fecha a sessão DMA via `VMMDLL_Close`), reinicia automaticamente o `StaticLoader.exe` recarregando todos os módulos do zero e exibe a notificação `"Static recarregado com sucesso!"`.
- **Changelog público (`devcaduh/StaticDMA`)**: sincronização automática deste `README.md` com o repositório público a cada nova atualização.

### `25/11/2026` — Static DMA & `StaticLoader.exe`
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
- **Rebranding**: atualizados cabeçalho, emblema principal, janela, rodapé e textos da aba `Session` (`Unload Static`).
- **Ícones Vetoriais Redesenhados**: todos os 15 ícones de abas e cartões e o emblema do topo refeitos com proporção e traço uniformes.
- **Remoção da Crosshair Roxa**: removida a mira fixa roxa central em `RenderFrame`.

### `24/11/2026` — Sistema de Configs Nomeadas
- Adicionados os controles `Name Config`, `Save Config` e a lista `Saved Configs` com botão vetorial `▶` (Play) para carregar perfis salvos em `%APPDATA%\River\configs\`.

### `24/11/2026` — Limpeza da Aba Configs
- Removida a box `Shortcuts` e o botão `Reset to defaults` da categoria `Configs → General`.

### `24/11/2026` — Auto-Updater Nativo Embarcado
- Implementado sistema de auto-atualização em C++20 nativo embarcado diretamente no binário (seções `.updt` / `.updd`, 100% compatível com ASLR), com tela dedicada de atualização, verificação SHA-1, substituição atômica sem `.bat` e notificação pós-atualização `"Atualizado com sucesso!"`.
