## Convenções do Payload 

Esta seção do padrão descreve as estruturas de carga útil de solicitação e resposta para todos os pontos de extremidade da API, bem como as convenções de nomenclatura para campos.

### Estrutura de Request do Payload

Cada carga de solicitação de API DEVE ter um objeto JSON no nível raiz conhecido como **root object**. Este objeto DEVE conter um objeto de `data` para armazenar os dados primários da solicitação.

O objeto raiz conterá um `meta` objeto se, e somente se, for especificamente NECESSÁRIO pelo ponto final. O metaobjeto é usado para fornecer informações adicionais, como dados de autorização do segundo fator, gerenciamento de tráfego, contagens de paginação ou outros propósitos complementares ao funcionamento da API.

A definição do conteúdo para o objeto `data` e o `meta` objeto será definida separadamente para cada ponto final.

### Estrutura de retorno do Payload

Cada carga de solicitação de API DEVE ter um objeto JSON no nível raiz conhecido como **root object**.

O conteúdo do objeto raiz é o seguinte:

* Se a resposta for bem-sucedida (200 OK), o objeto root:
    - DEVE conter um objeto `data`
    - DEVE conter um objeto `links`
    - PODE conter um objeto `meta`, se NECESSÁRIO pela definição do ponto final específico
* Se a resposta for malsucedida (não 200 OK), o objeto raiz:
    - PODE conter um objeto `erros` (conforme a definição específica do ponto final)
    
A definição do conteúdo para o objeto `data` e o `meta` objeto será definida separadamente para cada ponto final.

O objeto `links` conterá links para pontos finais da API relacionados. Isso incluirá links para oferecer suporte à paginação.

O objeto de links DEVE conter um campo chamado `self` que terá o URI totalmente qualificado para a solicitação atual como um valor.

O objeto `erros` será uma matriz de zero ou mais objetos não nomeados. Os campos em cada um desses objetos serão os seguintes:

* campo `code` DEVE estar presente: contém um código de erro específico do ponto final
* campo `title` DEVE estar presente: contém um rótulo legível por humanos do erro que é constante por código
* campo `detail` DEVE estar presente: contém uma descrição legível por humanos deste erro específico
* O objeto `meta` PODE estar presente: contém dados específicos adicionais sobre o ponto final relevantes para o erro

### Convenções de nomenclatura de campo

### Caracteres válidos em nomes de campos

Todos os nomes de campos definidos em uma carga útil de solicitação ou resposta DEVEM ser tratados com distinção entre maiúsculas e minúsculas por clientes e servidores e DEVEM atender a todas as seguintes condições:

* Os nomes dos membros DEVEM conter pelo menos um caractere.
* Os nomes dos membros DEVEM conter apenas os caracteres permitidos listados abaixo:
    - U+0061 to U+007A, a-z
    - U+0041 to U+005A, A-Z
    - U+0030 to U+0039, 0-9
* Além disso, os seguintes caracteres são permitidos nos nomes dos campos, exceto como o primeiro ou o último caractere:
    - U+002D HYPHEN-MINUS, '-'
    - U+005F LOW LINE, '_'
    - U+0024 DOLLAR SIGN, '$'

Qualquer outro caractere NÃO DEVE ser usado nos nomes dos campos.

### Estilo de nomeação de campo

Os nomes dos campos DEVEM ser nomes significativos com semântica definida.

Os campos que representam os mesmos dados em diferentes cargas úteis ou partes diferentes de uma carga útil DEVEM ter o mesmo nome.

Tipos de matriz devem ter nomes de campos no plural. Todos os outros nomes de campo DEVERÃO ser singulares.

Os nomes dos campos DEVEM ser definidos usando camel case com os seguintes esclarecimentos:

* Se um nome de campo é um acrônimo único, DEVE estar em minúsculas
* Se um nome de campo contiver um acrônimo junto com outras palavras, PODE estar em maiúsculas
* O primeiro caractere em um nome de campo DEVE ser minúsculo, a menos que faça parte de um acrônimo

Os campos NÃO devem ser nomeados usando tokens javascript reservados.

### Maps

Para JSON maps (ou seja, pares chave / valor), qualquer caractere Unicode PODE ser usado como nome de campo e requisitos estilísticos não se aplicam.

### Convenções de propriedade de campo

### Tipos de dados de campo


Cada campo definido para as cargas úteis de um ponto final DEVE ter um tipo de dados atribuído.

A lista de tipos de dados válidos é definida na seção [tipos de dados comuns](#common_field_types). Se um tipo de dados personalizado é necessário para um campo, o campo DEVE ser classificado como uma string com uma descrição clara de como o valor da propriedade deve ser interpretado ou definido.

### Campos Obrigatórios / Opcionais

Cada campo definido para as cargas úteis de um ponto final DEVE ter um status atribuído obrigatório, opcional ou condicional.

Os campos obrigatórios DEVEM estar presentes e ter um valor não nulo em uma carga útil de solicitação ou resposta para que a carga útil seja considerada válida.

Os campos opcionais PODEM estar presentes, mas isso não é garantido. Também é válido que esses campos estejam presentes, mas tenham um valor nulo. Observe que os campos opcionais indicam que os dados às vezes não podem ser mantidos por um titular de dados e este é um cenário esperado.

Os campos condicionais DEVEM ter uma declaração condicional associada. Se a declaração condicional for verdadeira em uma solicitação ou resposta específica, o campo será considerado obrigatório. Se a declaração condicional for falsa, o campo será considerado opcional.

*  Note that for optional fields are not considered optionally implementable by a Data Holder. For instance, if a Data Holder holds data in digital form for a Customer that is represented in a payload then it is expected that this data will be shared when authorised by the Customer. For payloads unrelated to Customers, such as product reference data, there is more discretion for the Data Holder but other drivers, such as complementary regulation or the requirement to align to other channels, should be taken into consideration. 

### Campos vazios / nulos

Um campo vazio (ou seja, um campo que não está presente em uma carga útil) será considerado equivalente a um campo que esteja presente com um valor `null`.

Uma sequência vazia (`“”`) não é considerada equivalente a `null`.

Um valor booleano de false não é considerado equivalente a `null`. Os campos booleanos opcionais, por implicação, têm três valores possíveis: verdadeiro, falso e indeterminado (ou seja, `null`).

### Convenções de objeto

Uma convenção específica será aplicada aos objetos de união.

Nos padrões, um objeto de união é usado em uma situação em que um conjunto de dados pode ser representado com diferentes conjuntos de campos, dependendo do contexto. Para manter a digitação forte dos campos, será usada uma de uma série de estruturas de objetos conhecidas. Um exemplo em que essa técnica é usada na norma está na definição de saldos de contas em que as informações do saldo podem ser representadas de maneira diferente, mas sem ambiguidade, para diferentes tipos de contas.

Para objetos de união, um campo adicional, com um sufixo conhecido, é usado para identificar o tipo de objeto que foi fornecido especificamente.

Como o nome desse campo é constante, ele pode ser usado para executar uma pesquisa indireta no tipo de objeto que foi realmente fornecido, removendo a necessidade de verificar qual objeto está presente.

Um campo desse tipo sempre será especificado com o sufixo `UType`, que significa Tipo de União.