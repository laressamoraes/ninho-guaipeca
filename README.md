# Ninho Guaipeca
Plataforma para tutores de pets, composta por um app Android e backend em microsserviços hospedado na AWS.

O projeto tem duas frentes:
* **Fórum:** tutores podem criar tópicos, interagir e tirar dúvidas entre si;
* **Bem-estar:** tutores conseguem encontrar veterinários, pet shops, banho e tosa e hospedagens próximos à sua localização, filtrando pela espécie do pet e pela nota dada pela comunidade.

**Guaipeca** é um termo que vem do tupi-guarani e é comumente utilizado no sul do Brasil para se referir a um cachorro, geralmente pequeno e vira-lata, um "bicho-solto".

Para além dessa definição, a ideia do projeto é englobar todas as espécies de pets e seus tutores. Por isso, o nome **Ninho**: o lugar onde toda a vida nasce e é cuidada, simbolizando a proteção, a segurança e o zelo.

Daí surge o **Ninho Guaipeca**, um aplicativo que reúne tudo o que os tutores precisam para garantir o bem-estar de seus companheiros inseparáveis.

## Funcionalidades planejadas
**Usuários**
* Cadastro e login com e-mail ou conta Google;
* Cadastro dos pets (espécie, raça, porte, idade).

**Fórum**
* Tópicos por categoria, com imagens;
* Comentários com respostas em thread;
* Votos positivos ou negativos em tópicos e comentários;
* Denúncia de conteúdo;
* Moderação automática de texto (lista de palavras proibidas) e de imagem (Amazon Rekognition);
* Notificação push quando um usuário responde um tópico.

**Bem-estar**
* Busca de estabelecimentos por raio a partir da localização do usuário;
* Filtro por tipo de serviço, espécie atendida e nota mínima;
* Avaliação dos estabelecimentos pela comunidade;
* Base de estabelecimentos importada do OpenStreetMap, completada por indicações de tutores.

## Decisões técnicas
* **Banco relacional com PostGIS:** os dados têm muitos relacionamentos (usuários, pets, tópicos, comentários, votos, avaliações), e o PostGIS faz a busca por distância direto no banco, com índice espacial.
* **Um schema por microsserviço:** os três serviços usam a mesma instância do RDS, mas cada um é dono do seu schema. Referências entre serviços (como autor de um tópico) são lógicas, sem chave estrangeira física.
* **UUID como identificador:** permite que um serviço referencie registros de outro sem depender de sequência do banco e evita expor IDs sequenciais nas URLs.
* **Nota média desnormalizada:** a média e o total de avaliações ficam no próprio estabelecimento, atualizados por trigger, para que a listagem filtre por nota sem JOIN e GROUP BY.
* **Localização aproximada:** o app usa a permissão de localização aproximada, suficiente para a busca por proximidade e menos invasiva para o usuário.
* **Moderação de texto por lista de palavras:** a detecção de toxicidade do Amazon Comprehend só suporta inglês, então o texto em português é moderado por uma listagem de palavras proibidas e denúncias da comunidade.
* **Estabelecimentos via OpenStreetMap:** a Overpass API é gratuita e os dados podem ser armazenados com atribuição. Uma Lambda agendada importa os dados semanalmente e os tutores cadastram o que faltar.
