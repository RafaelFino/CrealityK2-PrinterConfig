# Creality K2 — Configurações e Modificações

Este repositório reúne configurações e ajustes personalizados para a impressora **Creality K2**, incluindo perfis e configurações para modificações de hardware.

## DXC2 — Extrusora

A pasta `DXC2/` contém os arquivos de configuração desenvolvidos e ajustados para utilizar a extrusora DXC2 na Creality K2.

Chegar a uma configuração funcional exigiu um trabalho considerável de testes, ajustes e calibração, tanto na parte mecânica quanto nas configurações da impressora. Os arquivos disponibilizados aqui registram esse trabalho e servem como referência para reproduzir a configuração.

**Importante:** a instalação da DXC2 não deve ser tratada como uma simples substituição da extrusora original. Existem cuidados mecânicos e ajustes necessários para evitar problemas de montagem e funcionamento dos sensores.

## ⚠️ Preparação obrigatória antes da instalação

Antes de instalar a extrusora DXC2 na Creality K2, realize as seguintes verificações e modificações.

### 1. Base da extrusora e ímã do sensor Hall

Na base da extrusora DXC2, foi necessário remover o ímã localizado ao lado do sensor Hall utilizado pelo mecanismo de *cutter*.

Essa modificação foi necessária na configuração utilizada neste projeto para permitir o funcionamento adequado do conjunto.

Faça essa preparação antes de montar a extrusora na impressora.

### 2. Sensor de filamento — cuidado com os parafusos

O sensor de filamento é extremamente sensível à sua posição em relação à capa da extrusora.

**Não aperte excessivamente os parafusos de fixação do sensor.**

Se o sensor ficar próximo demais da capa da extrusora, poderá apresentar falhas de funcionamento. Durante a montagem, tenha cuidado com o aperto dos parafusos e verifique se o posicionamento final mantém a folga necessária.

Não presuma que apertar os parafusos até o limite seja a melhor forma de fixar o componente. O posicionamento correto é essencial para evitar problemas de detecção do filamento.

### 3. Impressão das peças de elevação

Antes de instalar a DXC2, imprima e instale as peças de elevação necessárias para a correia e para a entrada dos tubos de alimentação de filamento, localizadas na parte superior da extrusora.

Essas peças devem estar prontas e instaladas **antes da montagem da DXC2**.

Não deixe essa etapa para depois da instalação: a preparação prévia facilita a montagem e evita ter que desmontar o conjunto para corrigir esses pontos.

## Ordem recomendada de instalação

Para reproduzir a configuração documentada neste repositório, siga esta sequência:

1. Consulte os arquivos disponíveis na pasta `DXC2/`.
2. Imprima as peças de elevação da correia e da entrada dos tubos de alimentação. Prefira ABS, é uma peça que estará sujeita a altas temepraturas e forças mecânicas o tempo todo. PETG pode funcionar, PLA não é recomendado
3. Prepare a base da extrusora, removendo o ímã indicado junto ao sensor Hall do *cutter*.
4. Monte as peças de elevação na parte superior da extrusora.
5. Instale a DXC2 na Creality K2, tomando cuidado com a fixação e o posicionamento do sensor de filamento. Uma leve lubrificação com óleo adequado pode ajudar no cutter.
6. Aplique as configurações correspondentes aos arquivos disponibilizados neste repositório.
7. Verifique o funcionamento do sensor de filamento, do mecanismo de *cutter* e da alimentação do filamento.
8. Realize os testes e ajustes finais antes de utilizar a impressora normalmente.

## Configurações e responsabilidade

Os arquivos deste repositório refletem uma configuração específica da Creality K2 com a extrusora DXC2 e as modificações mecânicas descritas acima.

Resultados diferentes podem ocorrer dependendo das revisões de hardware, das peças utilizadas, da montagem e da versão do firmware.

Antes de aplicar qualquer configuração, confira os arquivos e certifique-se de que correspondem ao hardware instalado na sua impressora.

As modificações mecânicas devem ser feitas com cuidado. Sempre desligue a impressora antes de desmontar ou modificar componentes e verifique a segurança e o funcionamento do conjunto antes de retomar a operação.

## Objetivo deste repositório

Documentar as configurações, modificações e aprendizados necessários para utilizar a Creality K2 com a extrusora DXC2, facilitando futuras reinstalações, ajustes e a reprodução dessa configuração por outros usuários.
