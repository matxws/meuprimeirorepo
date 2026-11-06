# Além do Ninho

Abra `project.godot` no Godot 4.7 e execute o projeto (F5).

- **Novo jogo**: segue a ordem **prefácio → tutorial → primeira fase**. O prefácio em pixel art se baseia na introdução do GDD. São quatro cenas, com duração ajustada à narração gravada. Nilo aparece com seus irmãos amarelos na primeira cena. Os outros animais aparecem como easter eggs, sem nomes ou apresentação: uma tartaruga nadando, uma coelha correndo ao fundo, uma barragem num rio sinuoso entre as árvores ao fundo e uma coruja num galho sob a lua cheia.

O cenário do prefácio compartilha as texturas da primeira fase: planos com paralaxe, árvores variadas, chão florido e tufos ao vento. Lago e rio têm margens recortadas e reflexos animados; a cena noturna recebe tons frios e vagalumes.

- **Espaço**, no prefácio: avança uma cena. **Esc** ou o botão **Pular**: avança para o tutorial.
- **Ajuda / controles**: abre a aba de comandos no menu; **Voltar ao menu** ou **Esc** fecha a ajuda. Inclui o comando de planagem e os atalhos do prefácio.
- **Praticar tutorial**: abre o treino seguro e volta ao menu ao terminar, sem alterar a partida salva.
- **Áudio e ajustes**: volumes separados para música, vozes e efeitos, falas maiores e redução de efeitos visuais. As preferências ficam em `user://preferences.cfg`, separadas do progresso, e também podem ser alteradas na pausa.
- **Continuar**: retoma a partida mais recente, sem repetir o prefácio ou o tutorial. Fica indisponível enquanto não houver um salvamento válido.
- **A/D ou setas**: andar. **Espaço/W/↑**: pular. No ar, aperte **Espaço novamente e segure** para planar; solte para descer. **E**: conversar antes da amizade.
- **Esc**, durante a fase: abre a pausa, com **Continuar** e **Salvar e voltar ao menu**.

Há um único totem de salvamento, na plataforma mais alta do campo. Antes de ativá-lo, novas tentativas e o botão Continuar retornam ao início. Fazer amizade preserva Bia e seu bônus no arquivo, mas não cria um ponto de retorno na posição atual. Ao alcançar o totem e salvar com sucesso, ele acende e toca uma fanfarra; dali em diante, a retomada acontece no cume. Salvamentos antigos de plataformas intermediárias migram para o início, mantendo as amizades. Há uma única partida local, em `user://latest_game.json`; um novo jogo a substitui ao entrar na fase, depois do prefácio e do tutorial.


Para verificar o menu, o prefácio e o salvamento:

```sh
godot --headless --fixed-fps 120 --path . --script tests/menu_save_test.gd
```

Para verificar a persistência entre duas execuções separadas:

```sh
godot --headless --fixed-fps 120 --path . --script tests/persistence_process_test.gd -- write
godot --headless --fixed-fps 120 --path . --script tests/persistence_process_test.gd -- read
```

Os testes de salvamento usam arquivos próprios e preservam a partida do jogador.

Na fase atual, o chão termina antes da rota solo. O acesso atravessa um vão por plataformas suspensas, com mudanças de altura e saltos de precisão; a subida continua em pedras estreitas e alterna subidas e descidas. A travessia testada exige 14 saltos com o pulo normal. Cair no vão faz perder a tentativa e retornar ao início até ativar o totem do cume, com tentativas ilimitadas. A trilha não tem salvamentos intermediários. Com Bia, as duas plataformas principais, mais largas, continuam acessíveis em apenas dois saltos. A amizade continua opcional.


O GDD prevê também Tico (castor, pontes), Lola (tartaruga, proteção contra frutos) e Olívia (coruja, orientação no escuro). Bia e Tico já têm encontros jogáveis; Lola e Olívia pertencem às próximas fases.

Para conferir a travessia solo completa e a recuperação após quedas:

```sh
godot --headless --fixed-fps 120 --path . --script tests/solo_route_test.gd
godot --headless --fixed-fps 120 --path . --script tests/solo_failure_test.gd
```

A branch de síntese vocal inclui narração do prefácio e dublagem do encontro de Nilo e Bia, com controle de vozes no menu e na pausa. Os demais personagens têm amostras para avaliação. A geração, os perfis e os testes estão documentados em [tools/voices/README.md](tools/voices/README.md).

O campo agora tem camadas de cenário em paralaxe, aves animadas com cantos posicionais, rajadas visíveis, brisa baixa, passos na grama, pulo e aterrissagem. A grama se comprime sob os pés e se recupera depois, sem mudar as colisões. O controle **Sons** é separado de **Vozes**. Os efeitos e sua geração estão em [tools/audio/README.md](tools/audio/README.md).

O primeiro encontro de amizade com Bia toca um sino junto ao anúncio do pulo. O único totem toca uma fanfarra de metais após salvar. Os sons respeitam o controle **Sons** e não se repetem ao carregar a partida. Verifique com `tests/summit_rewards_test.gd`.

A plataforma da flor dourada evidencia a ajuda de Bia: seus 58 pixels de altura impedem o salto normal e permitem a subida em um salto com o bônus. O caminho solo continua pelas plataformas suspensas, sem degraus adicionais que encurtem a dificuldade. Bordas sólidas têm pontas claras, e uma pequena sombra marca o piso abaixo de Nilo durante o salto.

O pulo aceita o comando até 0,10 s depois de sair da borda e guarda um comando por 0,12 s antes de aterrissar. As tolerâncias não aumentam a força do salto, não permitem pulo duplo e não repetem saltos ao segurar a tecla. Conversa, recuperação e retorno da pausa limpam comandos pendentes.

Bia olha para Nilo ao descansar e pode dar dois pulos curtos de alegria na primeira plataforma, em silêncio. **“Parabéns, Niloo!”** só toca depois de ativar e salvar no totem, quando Bia também alcança fisicamente o cume. O balão tem uma camada própria, acima dos efeitos, e fica fora das áreas reservadas às mensagens. Depois da amizade, **E** não reabre a conversa nem mostra convite. Não há comentários acionados por R antes do totem.

Após um salvamento bem-sucedido, a moeda do totem sobe 12 pixels, gira três voltas e lança nove foguetes pixelados. As explosões se transformam nas letras de **VOCÊ PASSOU DE FASE!**, na parte superior da tela. **Progresso salvo!** aparece discretamente no rodapé. A fanfarra acompanha a animação, os controles continuam livres e a celebração funciona também no caminho solo. Falha de gravação, permanência no totem e carregamento de partida não repetem a fala nem a celebração.

Verificação dos controles, das reações e da celebração:

```sh
godot --headless --fixed-fps 120 --path . --script tests/jump_assistance_test.gd
godot --headless --fixed-fps 120 --path . --script tests/friendship_life_test.gd
godot --headless --fixed-fps 120 --path . --script tests/summit_rewards_test.gd
```

O menu usa por padrão `assets/audio/soundtrack/tela_inicial/tela_inicial.mp3`, em loop, sem ambiência. O prefácio usa `assets/audio/soundtrack/prefacio/prefacio.mp3` em volume baixo e com redução adicional durante as falas; a narração recebe +1 dB. **Sons** controla música e efeitos; **Vozes** continua independente. Avançar ou pular o prefácio libera seus áudios antes da entrada no tutorial.

No lago, Nilo nada com remadas alternadas, os pés submersos, flutuação suave e uma esteira de ondas. Os sons de remada seguem o mesmo ciclo da animação. Nos planos em terra há passos de grama; o rio permanece ao fundo apenas nas cenas do lago e da barragem. O vento e os pássaros pertencem ao prefácio e à fase, sem tocar no menu. À noite, os pássaros diurnos ficam em silêncio.

A interface usa o tema pixelado e as cenas da refatoração:

| Responsabilidade | Cena ou script |
| --- | --- |
| Navegação, transições, tela cheia e cliques | `scenes/app/scene_router.tscn` / `scripts/app/scene_router.gd` |
| Menu inicial | `scenes/screens/title/title_screen.tscn` |
| Prefácio e recursos de cada plano | `scenes/screens/preface/preface.tscn` |
| Tutorial seguro e lições físicas | `scenes/levels/tutorial/tutorial.tscn` / `scripts/levels/tutorial.gd` |
| Preferências e canais independentes | `scripts/state/game_settings.gd` / `scenes/ui/settings/settings_panel.tscn` |
| Indicador de aterrissagem compartilhado | `scripts/environment/landing_marker.gd` |
| Enquadramento inicial do totem | `scripts/levels/goal_intro.gd` |
| Encerramento e repetição da fase | `scenes/ui/level_end/level_end.tscn` |
| HUD, pausa e aviso de falha | `scenes/ui/gameplay_ui.tscn` |
| Diálogo e habilidade de pulo | `scenes/ui/conversation/conversation.tscn` / `scenes/ui/jump_reward/jump_reward.tscn` |
| Controles de áudio compartilhados | `scenes/ui/audio_settings/audio_settings.tscn` |
| Sons sincronizados do prefácio | `scenes/audio/preface_foley.tscn` |
| Totem único | `scenes/levels/props/checkpoint.tscn`, instanciado em `Checkpoints/Summit` |

Para conferir música, prioridade da narração, troca de sons entre água/terra e liberação dos áudios:

```sh
godot --headless --fixed-fps 120 --path . --script tests/preface_audio_test.gd
godot --headless --fixed-fps 120 --path . --script tests/ui_click_sound_test.gd
```

A planagem bate as asinhas em doze poses pixeladas, na mesma escala e paleta do corpo, com a raiz integrada ao flanco e recuperação mais lenta. Ela reduz a velocidade de queda para até 60 px/s, por até 0,9 s de descida em cada salto. Ela mantém a direção controlável e não aumenta a altura do pulo: o bônus de Bia continua útil para alcançar as plataformas altas. O primeiro Espaço só pula; é necessário soltar e apertar novamente no ar. Aterrissar recupera o fôlego; conversa, pausa e recuperação fecham as asas e exigem liberar a tecla. Segurar Espaço ao tocar o chão não causa um novo pulo automático. Há uma dica de comando durante o salto.

A última cena do prefácio toca um canto suave de coruja, enquanto os pássaros diurnos permanecem em silêncio. O canto para ao sair da cena, pular o prefácio ou desligar os sons. O áudio original é gerado por `tools/audio/generate_owl.py`.

```sh
godot --headless --fixed-fps 120 --path . --script tests/glide_test.gd
godot --headless --fixed-fps 120 --path . --script tests/preface_audio_test.gd
```

No menu inicial, cada animal tem sua dança: Nilo alterna as patas e bate as asas, Bia dá saltinhos com as orelhas acompanhando o movimento e solta poucos grãos de terra de um pixel ao sair do chão e pousar, Tico vira de costas, alterna os passinhos e bate a cauda larga no chão, lançando torrões que caem e se assentam; ao erguer a cauda, solta grãos menores. Olívia decola com poeira leve, paira batendo as asas, vira a cabeça para olhar por cima do ombro e pousa levantando uma nuvem curta de poeira. Lola faz break dance: recolhe cabeça e patas, gira dentro do casco soltando poeira continuamente e sai novamente. Os patinhos amarelos aparecem apenas no prefácio. O elenco é apenas ilustrado: os encontros e as amizades continuam acontecendo nas fases. O prefácio mantém seus quatro planos originais.

As aves da fase voam em trajetórias no espaço do cenário, independentes da câmera, inclusive quando Nilo cai em um vão.

Ao fazer amizade com Bia, a conquista mostra apenas uma pegada pixelada e a porcentagem real do bônus em dourado (+39%), por 1,8 s. No menu, Lola dá um pulinho antes de se recolher no casco, com um pequeno efeito pixelado e poeira de pouso.

Na primeira cena do prefácio, Nilo e os três irmãos soltam oito quacks baixos e espaçados. Os bicos abrem e fecham em pixels sincronizados com as chamadas, acompanhando a cabeça durante a natação. São efeitos de **Sons**, independentes do controle de **Vozes**, e param ao avançar ou pular a cena.

O tutorial começa em uma tela preta com **BEM-VINDO AO TUTORIAL!** em branco pixelado. São cinco lições: ir para a frente, voltar, pular e pousar, alinhar a bolinha de aterrissagem com o alvo dourado e atravessar um rio planando (soltando/apertando Espaço novamente) até um totem misterioso na outra margem. Um salto comum não vence a distância; cair na água devolve Nilo à margem para tentar novamente, sem dano. Planar parado não conclui a lição. Na primeira lição, somente D/→ movimenta Nilo. Na segunda, os dois sentidos ficam disponíveis e o pulo permanece bloqueado. Nas lições de pulo e da bolinha, andar e pular estão liberados, mas planar só fica disponível na quinta lição. Ao concluir as cinco lições, aparece **PARABÉNS! Você concluiu o tutorial com sucesso!** enquanto a moeda do totem sobe e lança fogos formando **TUTORIAL CONCLUÍDO!** no centro da tela. Nilo dança de um lado para o outro, ao som da fanfarra de trompetes, antes de a primeira fase começar automaticamente (a prática retorna ao menu, preservando a campanha). A progressão depende das ações físicas; as tentativas não causam dano. O indicador mostra a superfície logo abaixo dos pés, acompanhando o movimento no ar. É o mesmo indicador da fase. **Pular tutorial** segue para a aventura; em modo de prática, volta ao menu.

Ao iniciar a fase após o treino (ou escolher **Jogar novamente**), a câmera apresenta brevemente o totem com **Alcance o totem misterioso** e devolve os controles a Nilo. Ao vencer, moeda, fogos e fala de Bia terminam antes das opções **Próxima fase: o rio do Tico**, **Jogar novamente** e **Voltar ao menu**. Repetir reinicia a primeira fase e as amizades; voltar preserva o salvamento concluído. **Continuar** em um salvamento do cume oferece essas opções sem repetir a comemoração.

**Falas maiores** aumenta texto e espaço dos balões, incluindo narração e a fala de Bia. **Reduzir efeitos visuais** remove partículas, vento em linhas, giro decorativo da moeda e fogos; congela as danças do menu e simplifica zooms e enquadramentos. Os comandos, a bolinha e as animações de movimento necessárias ao jogo continuam disponíveis.

```sh
godot --headless --fixed-fps 120 --path . --script tests/tutorial_test.gd
godot --headless --fixed-fps 120 --path . --script tests/accessibility_test.gd
godot --headless --fixed-fps 120 --path . --script tests/level_end_test.gd
```

### Organização por cenas e responsabilidades

As cenas `.tscn` são as raízes de composição: o editor mostra os componentes persistentes de cada tela, e os scripts do nível conectam esses componentes. Interface, animação e persistência permanecem separados.

| Responsabilidade | Cena / código |
| --- | --- |
| Tutorial: lições, bloqueios e tentativas | `scenes/levels/tutorial/tutorial.tscn` → `scripts/levels/tutorial.gd` |
| Tutorial: textos, boas-vindas e botões | `scenes/ui/tutorial/tutorial_ui.tscn` → `scripts/ui/tutorial_ui.gd`; emite intenções por sinais |
| Tutorial: vitória e duração da comemoração | nó `Completion` → `scripts/levels/tutorial_completion.gd`; emite `finished` para o nível |
| Tutorial: alvo e rio | nós `TargetMarker` e `GlideRiver` → `scripts/levels/tutorial_target.gd` e `tutorial_river.gd` |
| Primeira fase: sessão, falhas e totem | `scenes/levels/main.tscn` → `scripts/levels/main.gd`; cenário, grama, marcador, ambiente e áudio são nós explícitos |
| Menu e prefácio | `scenes/screens/title/` e `scenes/screens/preface/`; controle de tela separado da ilustração e do áudio |
| Danças por personagem | `scripts/characters/dances/`; cada animal controla seus movimentos e contatos com o solo. A dança de Lola permanece em `scripts/screens/lola_dance.gd` |
| Dança de vitória e asas do jogador | `scenes/characters/effects/`; reutilizam os pixels das animações, sem alterar a física |
| Preferências, campanha e navegação | autoloads `GameSettings`, `GameSession` e `SceneRouter`; componentes visuais não escrevem o save |

Os comentários em português destacam os contratos importantes: liberar comandos por lição, descartar progresso de uma travessia falha, aguardar a física antes do primeiro save e aguardar os efeitos antes de trocar de cena. A navegação mantém o esquema de salvamento existente.

### Segunda fase: o rio do Tico

Branch de desenvolvimento: `feat/fase-tico`, criada a partir da `main`. Depois da comemoração da primeira fase, **Próxima fase: o rio do Tico** inicia uma caminhada curta com conversa. Bia aparece se a amizade foi feita; os amigos e o bônus de pulo permanecem na segunda fase.

O rio tem mais de mil pixels de largura: nem o superpulo com planagem atravessa diretamente. A missão exige recuperar o mapa perdido e chegar à outra margem. Sem amizade com Tico, Nilo atravessa por oito jacarés que nadam lateralmente, usando o sprite fornecido. O contato dispara o bote, a abertura da mandíbula, respingos e som de água; cair encerra a tentativa com respingos, afundamento, bolhas e retorno ao checkpoint salvo. Bia e Tico, quando amigos, usam um controlador compartilhado para seguir por apoios móveis e alcançar as margens sem teleporte. O controlador padrão dos animais é desativado depois do `_ready` do pai, evitando gravidade e movimento duplicados.

Ao aceitar a amizade, Tico monta um veleiro no cenário em cerca de seis segundos: toras, amarrações, mastro e vela, com martelo, serrote e poeira. **E** perto do barco inicia o embarque no próprio cenário: o casco espera em terra, os amigos sobem e Tico usa uma vara para deslocar o veleiro sobre roletes até a água. A câmera de perseguição em 3D assume somente depois do lançamento. **A/D ou setas** escolhem entre três pistas; um timão de madeira gira no sentido da manobra, as cordas tensionam, a vela enche e o vento é panoramizado para o lado da curva. O barco percorre fisicamente o rio com câmera de perseguição; margens, árvores e destroços permanecem no mundo. A água usa correnteza pixelada, ondulações e esteira. Três impactos destroem progressivamente o casco: as próprias toras se desprendem, levantam lascas de madeira e ficam flutuando para trás. O mapa flutua no rio: passar por ele o recolhe; se for perdido de vista, ele volta à frente. Uma pista livre é preservada nas sequências de jacarés.

A caminhada de transição usa cenários em pixel art com paralaxe, árvores restritas à terra, vento visível, aves e falas sintetizadas até a margem. O veleiro mostra os passageiros de costas e jacarés de frente, abrindo a mandíbula na aproximação. A construção exibe cavaletes, toras assentadas individualmente, amarrações, içamento do mastro, ferramentas e poeira; o barco desce gradualmente dos apoios até o chão e aguarda o embarque. A cachoeira tem pedras, água animada e som contínuo. Vento, correnteza, nado, botes, ferramentas e madeira quebrando respeitam o volume de efeitos. A captura do mapa é salva; após uma falha do barco, a missão continua com as amizades e a construção preservadas. **Esc** retorna ao menu. **Continuar** reconhece a segunda fase e restaura a margem correspondente, preservando os saves antigos da primeira.

As cenas ficam em `scenes/levels/river/` e `scenes/characters/river/`. O diretor `river_level.gd` coordena a missão; `boat_workshop.tscn` e `boat_build.gd` fazem a montagem por etapas usando o mesmo modelo navegável; `boat_run.gd` controla as pistas e colisões; `sailboat.gd` cuida da geometria e das cordas; `wooden_helm.tscn` e `wooden_helm.gd` compõem o timão de aro, raios e pegadores; `boat_debris.gd` simula toras e lascas; `swimming_crocodile.gd` e `river_directional_art.gd` desenham a perspectiva frontal e as costas dos passageiros; `water_fall.gd` coordena o mergulho e o retorno; `river_course_3d.gd`, `river_backdrop.gd` e `river_audio.gd` cuidam do ambiente. A interface do barco tem sua própria cena, `boat_hud.tscn`. O veleiro usa o PNG `assets/personagens/barco/raft_sail.png` na montagem e como textura da madeira e da vela deformável em 3D; os jacarés de plataforma usam o PNG original fornecido e os do veleiro têm poses frontais próprias em pixel art. Os sons originais podem ser regenerados com `python3 tools/audio/generate_river.py`.

Testes da fase: `tests/tico_phase_test.gd`, `tests/tico_solo_test.gd` e `tests/tico_boat_controls_test.gd` e `tests/tico_river_limits_test.gd`. Eles cobrem a entrada pela tela de vitória, amizades e construção, rotas físicas, direção e panorama estéreo, dano e nova tentativa, mapa, compatibilidade do save e retomada da segunda fase.

Aves das duas fases podem realizar rasantes ocasionais: aviso vermelho escuro pulsante por 1,3 segundo, descida em curva, um empurrão sem dano direto e subida de volta à trajetória. O destino é fixo no mundo: elas não perseguem quedas ou respawns. `scripts/environment/bird_dives.gd` controla essa sequência; o modo de reduzir efeitos mantém o aviso vermelho estável. Os testes `bird_dive_test.gd`, `river_presentation_test.gd` e `river_companion_route_test.gd` cobrem aviso/empurrão, falas, interface, afundamento, respawn e travessia dos dois amigos.

A água do velejo usa camadas de correnteza, cristas curvas, reflexos do céu e espuma nas margens em uma paleta pixelada. Proa e esteira em V são desenhadas na própria superfície, acompanhando a posição real do barco. `tests/sailing_helm_test.gd` verifica a rotação do timão nos dois sentidos, resposta das cordas, panorama do vento, alinhamento da esteira e redução de efeitos.

### Direção de arte compartilhada e chegada ao rio

`meadow_style.gd` define a paleta do prefácio e o desenho comum de montanhas, árvores, tufos e cachoeiras. Prefácio/menu, tutorial, primeira fase, caminhada de transição e rio reutilizam esse vocabulário. `scenes/environment/terrain_finish.tscn` reveste as geometrias existentes com terra, pedra, raízes e bordas de grama; os tamanhos e as colisões permanecem nas cenas originais. A versão 3D reutiliza os mesmos desenhos nas montanhas de `river_vista.tscn`, com cachoeira de 34 unidades de altura, névoa, clareira e trilha de chegada. O som da queda d'água cresce na aproximação.

`boat_launch.tscn` coordena caminhada até o casco, embarque e lançamento. `boat_arrival.tscn` coordena aproximação final, alinhamento, prancha e desembarque de cada passageiro. O final agora depende de todos estarem em terra; o salvamento e a mensagem de vitória aparecem depois, sem escurecer a paisagem. A câmera abre para mostrar a cachoeira inteira e a margem. Ao continuar, personagens e barco são restaurados na margem de chegada. Se o jogador perder o mapa durante o velejo, há uma última oportunidade segura na pista central antes de atracar.

`boat_lifecycle_test.gd` verifica as etapas de embarque, lançamento, atracação, desembarque e salvamento. `boat_map_recovery_test.gd` cobre a recuperação final do mapa; `meadow_style_test.gd` verifica o uso dos elementos compartilhados e a preservação das colisões.

### Sol, iluminação e continuidade do cenário

A primeira fase tem sol de meio-dia, alto e branco, céu azul e sombras curtas junto aos pés. As árvores grandes adicionadas à frente do cenário foram removidas; a vegetação original de fundo permanece. A caminhada para o rio e toda a segunda fase acontecem ao entardecer: sol baixo, céu quente, sombras alongadas, montanhas com tons quentes e reflexos dourados na água. `sun_palette.gd` compartilha as cores entre o cenário 2D e o velejo; `sunlight_2d.tscn` aplica a luz ao mundo e projeta sombras sobre o chão real, preservando o contraste da interface. A terceira fase ainda não foi implementada.

As montanhas usam identificadores fixos pela posição no mundo. Mover a câmera muda apenas seu enquadramento, sem trocar o formato. Os dois companheiros preservam sua ordem espacial ao caminhar e inverter a direção; não pulam para ultrapassar um ao outro. Na travessia, ele aguarda a liberação do próximo apoio. O embarque conserva a ordem dos personagens, com espaço para os três no casco, e o desembarque os libera um por vez. Na margem de chegada, o barco permanece na água, separado dos amigos em terra.

`lighting_scenery_test.gd` verifica a remoção das árvores grandes, os dois perfis de iluminação e a preservação das cores da interface. `companion_spacing_test.gd` verifica a separação dos corpos ao seguir, parar e inverter a direção; `river_companion_route_test.gd` e `boat_lifecycle_test.gd` também verificam a separação na travessia e no embarque.

Tico começa deitado sobre uma pilha de toras em `tico_woodpile.tscn`. Ao apertar **E**, ele acorda, caminha até a borda e desce pela gravidade; a conversa começa depois do pouso. Ao aceitar a amizade, essa mesma reserva de madeira diminui durante toda a construção, até acabar quando o veleiro fica pronto. Saves com barco construído não repetem o descanso nem restauram a madeira consumida. `tico_woodpile_test.gd` cobre a descida com controles reais e o consumo gradual. A voz original de Tico continua sendo usada, com ressonância nasal ajustada nos clipes e no perfil de geração.

### Totem da segunda fase e noite na clareira

Após atravessar o rio e recuperar o mapa, caminhe até o **totem da outra margem**, no fim da fase. A moeda sobe, gira e lança os fogos de **VOCÊ PASSOU DE FASE!**. Bia e Tico, quando recrutados, dançam usando as animações do menu e dão os parabéns com suas próprias vozes. O componente `river_completion.tscn` aguarda os amigos chegarem fisicamente antes da festa; as opções **Seguir viagem** e **Voltar ao menu** aparecem depois das falas. O save recebe o campo opcional `level_progress.totem_reached`, sem mudar a versão ou rejeitar saves antigos. Ao continuar uma partida com o totem concluído, as opções reaparecem sem repetir a recompensa.

**Seguir viagem** abre `scenes/levels/camp/camp_transition.tscn`: o grupo chega caminhando a uma clareira enquanto anoitece, acende a fogueira, abre o mapa e conversa sobre o Festival do Lago. O mapa recuperado é a rota para o festival. Depois, todos dormem em uma passagem de tempo acelerada de seis segundos: a lua desce até o horizonte, a fogueira vira brasa e o sol nasce. Céu, montanhas, vegetação, chão e personagens clareiam continuamente. Os pássaros retornam um por vez, com aparição gradual e cantos suaves desde o início do amanhecer; então o grupo acorda e retoma a caminhada. À noite, cantos de coruja e uivos distantes se alternam e diminuem durante o nascer do sol, respeitando o controle de efeitos. O perfil de Tico acompanha o sentido do deslocamento e suas passadas são sincronizadas com a distância percorrida. A cena respeita as amizades conquistadas, os volumes separados e as preferências de acessibilidade. **Espaço/Enter** avança as falas e **Pular cena** vai ao amanhecer. O último checkpoint jogável é preservado; a cena final oferece retornar ao totem ou ao menu.

Conforme a tabela **Personagens e fases**, na página 4 do GGD fornecido (`sggd (1).pdf`), a terceira fase é **Pomar do Vento**, com **Lola, a tartaruga**. Seu casco protege o grupo dos frutos; a alternativa solo usa abrigos de troncos e espera pelos intervalos entre as quedas. A transição apresenta esse próximo destino; a jogabilidade do pomar ainda não está implementada.

A direção fica em `camp_transition.gd`; `camp_backdrop.gd`, `camp_cast.gd`, `camp_fire.gd` e `camp_audio.gd` cuidam respectivamente do cenário, poses, chama e sons. `camp_dialogue.gd` reúne as falas incluídas no catálogo de vozes offline. `tools/audio/generate_camp.py` gera o crepitar original e `tools/audio/generate_howl.py` gera o uivo distante. `river_completion_test.gd` cobre o totem, a festa, a compatibilidade do save e a transição; `camp_transition_test.gd` cobre o anoitecer, a conversa, o descanso, o amanhecer e a composição do grupo. `camp_ambience_test.gd` verifica a aparição gradual das aves, os cantos ao amanhecer, a alternância dos sons noturnos e o controle de volume.
