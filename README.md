# QR://DOOM — FORBIDDEN BUILD v0.13.7

> *"Por que isso existe?"* — Todo programador que abriu esse arquivo.

```
██████╗ ██████╗     ██╗██╗   ██╗    ██████╗  ██████╗  ██████╗ ███╗   ███╗
██╔═══██╗██╔══██╗    ██║██║   ██║    ██╔══██╗██╔═══██╗██╔═══██╗████╗ ████║
██║   ██║██████╔╝    ██║██║   ██║    ██║  ██║██║   ██║██║   ██║██╔████╔██║
██║▄▄ ██║██╔══██╗    ██║╚██╗ ██╔╝    ██║  ██║██║   ██║██║   ██║██║╚██╔╝██║
╚██████╔╝██║  ██║    ██║ ╚████╔╝     ██████╔╝╚██████╔╝╚██████╔╝██║ ╚═╝ ██║
 ╚══▀▀═╝ ╚═╝  ╚═╝    ╚═╝  ╚═══╝      ╚═════╝  ╚═════╝  ╚═════╝ ╚═╝     ╚═╝
```

**QR://DOOM** é um jogo de tiro em primeira pessoa inspirado no DOOM clássico de 1993, onde **o framebuffer inteiro é uma grade de QR code animada**. Cada pixel na tela é um módulo QR. O mundo 3D é renderizado através de um sistema de raycasting cujo output é convertido em padrões binários de preto e branco — exatamente como um QR code real — mas em tempo real, 60 frames por segundo, com inimigos, tiros e corrupção de dados.

Este projeto foi **gerado inteiramente pelo [Claude](https://claude.ai), o modelo de IA da [Anthropic](https://www.anthropic.com)**, a partir de um único prompt descrevendo a ideia. Nenhuma linha de código foi escrita manualmente por um humano.

---

## Demonstração

| Estado normal | Combate (corrupção alta) | Finder patterns no canto |
|---|---|---|
| QR estável, inimigos como glifos | Bits se invertendo, scanlines | Estrutura QR real visível |

O jogo parece **software proibido recuperado de um servidor abandonado**. O desenvolvedor original não foi localizado. Todos os QR codes escaneados retornaram 404.

---

## Por que isso existe

Um QR code é um padrão criado para armazenar dados estáticos que scanners leem. Usar sua estrutura de grid como framebuffer de um raycaster em tempo real é:

- Tecnicamente desnecessário
- Visualmente perturbador
- Impossível de escanear
- Completamente funcional como jogo

Exatamente esse é o ponto.

---

## Funcionalidades

### Jogo
- Movimento em primeira pessoa com raycasting (técnica do DOOM original)
- WASD + mouse para mover e olhar
- Q / E para strafe (deslocamento lateral)
- Sistema de tiro hitscan
- Inimigos com IA de perseguição e colisão
- Sistema de HP e dano
- Ondas de inimigos com dificuldade crescente
- +25 HP de cura entre ondas
- Minimapa no canto da tela
- HUD minimalista com HP, armadura, kills e wave

### Renderização
- **Framebuffer de luminância** — o mundo 3D é calculado como valores float de 0.0 a 1.0
- **Dithering ordenado de Bayer 4×4** — converte brilho em módulos binários (cheio/vazio)
- **Overlay de estrutura QR real** — Finder Patterns, Timing Patterns e Alignment Pattern são estampados sobre a cena
- **Codificação de estado do jogo** — HP e kills são literalmente codificados em binário nos módulos da borda da tela
- **Corrupção procedural** — inversões de bit, glitch de linhas, smear de colunas
- **Efeitos visuais** — scanlines CRT, vinheta, flash de dano, fósforo verde

### Inimigos como glifos QR
Cada tipo de inimigo é um padrão de bits 8×8 projetado e escalado na cena:

| Inimigo | Cor | HP | Dano | Comportamento |
|---|---|---|---|---|
| DRONE | Verde | 35 | 8 | Rápido, básico |
| SPECTER | Amarelo | 65 | 16 | Moderado |
| CORRUPTOR | Laranja | 130 | 28 | Lento, destrutivo |

Quando você se aproxima de um inimigo, os módulos QR ao redor distorcem. Quando você atira, os bits do glifo se corrompem. Quanto mais combate, mais a estrutura QR da tela se degrada.

---

## Pipeline de Renderização

```
Estado do Mundo
      │
      ▼
  Raycaster (DDA)
      │  Um raio por coluna de tela
      │  Calcula distância, altura e brilho de parede
      ▼
Buffer de Luminância (Float32Array)
      │  Valores 0.0 a 1.0 por módulo
      │  Sprites de inimigos injetados com glifos 8×8
      ▼
Dithering de Bayer 4×4
      │  Float → binário (0 ou 1)
      │  Cria padrão de meios-tons autêntico
      ▼
Overlay de Estrutura QR
      │  Finder Patterns nos 3 cantos
      │  Timing Patterns entre finders
      │  Alignment Pattern (corrompido em combate)
      │  Estado do jogo codificado nas bordas
      ▼
Corrupção Global
      │  Flip de bits aleatório
      │  Glitch de linhas e colunas
      │  Efeitos de HP crítico
      ▼
Canvas (ImageData)
      Bloco MSIZE×MSIZE por módulo
      Verde fósforo (#00d232)
      Borda escura por módulo (gap QR autêntico)
```

---

## Como Rodar

**Requisito: zero.** Nenhuma dependência, nenhum servidor, nenhum npm, nenhum build.

1. Baixe o arquivo `QRDOOM_v2.html`
2. Abra no navegador (Chrome ou Firefox recomendado)
3. Aguarde a sequência de boot
4. Pressione qualquer tecla para iniciar

```bash
# Ou via terminal, se preferir:
open QRDOOM_v2.html        # macOS
xdg-open QRDOOM_v2.html   # Linux
start QRDOOM_v2.html       # Windows
```

É um único arquivo HTML de ~500 linhas. Nada mais.

---

## Controles

| Tecla | Ação |
|---|---|
| `W` / `↑` | Mover para frente |
| `S` / `↓` | Mover para trás |
| `A` / `←` | Girar à esquerda |
| `D` / `→` | Girar à direita |
| `Q` | Strafe esquerda |
| `E` | Strafe direita |
| `Mouse` | Olhar (clique no canvas primeiro para capturar) |
| `Clique esquerdo` | Atirar |
| `Espaço` | Atirar |

---

## Estrutura do Código

O projeto é um único arquivo HTML com JavaScript puro. As principais seções são:

```
Constantes e configuração
Map (grid 22×18 de paredes)
Estado do jogador
Tipos de inimigos (glifos 8×8)
Framebuffer (LUM + QRB + ZDEPTH)
─── Raycaster (castRays)
─── Projeção de sprites (renderSprites)
─── Renderização da arma (renderWeapon)
─── Conversão luminância → QR (lumToQR)
─── Overlay de estrutura QR (stampQRStructure)
─── Codificação de estado (encodeStateInBorder)
─── Corrupção global (applyCorruption)
─── Saída para canvas (renderToCanvas)
─── Minimapa (renderMiniMap)
Input (teclado + mouse + pointer lock)
IA dos inimigos (updateEnemies)
Movimentação do jogador (updatePlayer)
Lógica de ondas (updateWave)
Sequência de boot
Loop principal (requestAnimationFrame)
```

---

## Detalhes Técnicos

**Resolução de jogo:** 720×480 px (CSS) → 180×120 módulos QR × 4 px por módulo

**Raycasting:** DDA (Digital Differential Analysis), mesma técnica usada no DOOM original e no Wolfenstein 3D. Um raio por coluna de módulo, sem divisão de mapa por setores.

**Dithering:** Matriz de Bayer 4×4 aplicada sobre o buffer de luminância. Garante que paredes próximas tenham módulos densos e paredes distantes tenham módulos esparsos — sem gradientes de cor, apenas binário.

**Sprites:** Projetados com transformação de câmera inversa. Oclusão por Z-buffer de profundidade por coluna.

**Corrupção:** Escala com intensidade de combate (`globalCorruption` 0.0–1.0). Decai naturalmente em momentos de calma. Em HP crítico (<25), a região central da tela inverte periodicamente.

**QR real:** Os Finder Patterns, Timing Patterns e Alignment Pattern estampados são estruturalmente corretos conforme ISO/IEC 18004. Eles apenas não podem ser escaneados porque o conteúdo dos módulos é um jogo 3D em execução.

---

## Sobre a Criação

Este projeto foi criado pelo **Claude Sonnet**, modelo de inteligência artificial desenvolvido pela **[Anthropic](https://www.anthropic.com)**.

O prompt original descrevia um jogo DOOM renderizado através de QR codes animados, tecnicamente absurdo mas funcional. O Claude projetou a arquitetura completa de renderização, o pipeline de conversão luminância → dithering → módulos QR, os glifos de inimigos, o sistema de corrupção procedural e todos os efeitos visuais — e gerou o código inteiro como um único arquivo HTML sem dependências externas.

> Claude é acessível em [claude.ai](https://claude.ai) e via [API da Anthropic](https://www.anthropic.com/api).

---

## Licença

MIT. Faça o que quiser com isso. O desenvolvedor original não foi localizado.

```
QR MATRIX STATUS: CURSED
SYSTEM INTEGRITY: [████░░░░░░] 42%
CORRUPTION LEVEL: ACCEPTABLE
```

---

*Este software não deveria existir. E ainda assim aqui estamos.*
