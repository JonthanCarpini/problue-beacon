# problue-beacon

Espelho público do **beacon** do PROBLUE PDV & ERP: a lista assinada de endereços que o aplicativo
instalado consulta para descobrir onde a API está viva.

Existe para o aplicativo **não depender de um único domínio**. Se um endereço for bloqueado ou
perdido, basta publicar aqui uma versão nova e todos os PDVs migram sozinhos, sem atualização.

## O que tem aqui

Só `beacon.json`. Nenhum segredo: são endereços públicos.

O documento é assinado com Ed25519. O aplicativo traz a chave **pública** embutida e **descarta
qualquer beacon sem assinatura válida** — é isso que impede alguém de desviar os PDVs para um
servidor falso ao dominar este repositório.

O campo `v` é uma versão que **só cresce**. O aplicativo recusa documento com versão menor ou
igual à última que aceitou, para que um beacon antigo capturado não possa ser servido de novo.

## Como atualizar

Não edite este arquivo à mão: a assinatura deixaria de bater e o aplicativo ignoraria o conteúdo.

1. No servidor, ajuste `BEACON_HOSTS_API` e **aumente** `BEACON_VERSAO` em `deploy/.env`.
2. Recrie o app e baixe o documento novo:
   `curl -s https://problue.sistemas.digital/beacon.json -o beacon.json`
3. Faça commit aqui.
4. Limpe o cache da jsDelivr, que segura o arquivo antigo por horas:
   `curl -s https://purge.jsdelivr.net/gh/JonthanCarpini/problue-beacon@main/beacon.json`
   O GitHub raw se atualiza sozinho em alguns minutos.

Espelho atrasado não quebra nada: o aplicativo consulta as quatro fontes e fica com a de **maior
versão**. O espelho desatualizado só deixa de ajudar se o resto cair.

## Fontes que o aplicativo consulta

| Fonte | Por quê |
|---|---|
| `problue.sistemas.digital/beacon.json` | a mais fresca |
| `pdvoff.pdvtotal.site/beacon.json` | outro domínio registrado, sobrevive à perda do primeiro |
| jsDelivr (este repositório) | fora da nossa infraestrutura |
| GitHub raw (este repositório) | fora da nossa infraestrutura |
