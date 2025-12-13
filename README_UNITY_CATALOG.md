# Unity Catalog

![alt text](UNITY_CATALOG_IMAGES/image-5-v.png)


### Curso: Udemy - Azure Databricks end to end project with Unity Catalog CICD

<p align="center">
  <blockquote>
Atua como uma interface unificada para os workspaces em relação a User Management, Metastore, Acess Controls.  
E também pode ver quem tem acesso a quê. Tem Acess Control, Lineage, Discovery, Monitoring, Auditing, Sharing.  
Facilita o gerenciamento e governança principalmente quando tenho vários workspaces e tenho que centralizar o permissionamento.
  </blockquote>
</p>

![alt text](UNITY_CATALOG_IMAGES/image-1.png)

![alt text](UNITY_CATALOG_IMAGES/image-2.png)

## Databricks Access Connector

O Databricks Access Connector, um serviço que funciona como identidade gerenciada para permitir que o Unity Catalog / Metastore tenha acesso centralizado ao Azure Data Lake Gen2.

Como o Unity Catalog não é um recurso do Azure e não pode receber permissões diretamente via IAM, é necessário criar uma identidade gerenciada — o Databricks Access Connector.
Depois, essa identidade recebe o papel Storage Blob Data Contributor na conta de armazenamento.

Assim, o Unity Catalog poderá usar essa identidade para acessar o Data Lake e criar o Metastore, que é onde serão armazenadas tabelas gerenciadas e configurações de governança.

![alt text](UNITY_CATALOG_IMAGES/image-3.png)

Passos do vídeo:

1. Criar o Databricks Access Connector.

* Obs: Quando é criado um workspace no databricks já e criado um access connector denominado: unity-catalog-access-connector, então se não desejar, não precisa criar um novo.

- Copie o Resource ID depois que foi criado

<img src="UNITY_CATALOG_IMAGES/image-7.png" width="50%">
<img src="UNITY_CATALOG_IMAGES/image-4.png" width="70%">

2. Atribuir a ele o papel Storage Blob Data Contributor no ADLS Gen2.

<img src="UNITY_CATALOG_IMAGES/image-5.png" width="70%">

3. Esse ID será usado depois para criar o Metastore no Unity Catalog.

![alt text](UNITY_CATALOG_IMAGES/image-2-v.png)

---

Para o databricks com UC, primeiramente eu preciso de Databricks Premium Workspace onde também e necessário configurar um metastore. Portanto, ao configurar o metastore, ele nos perguntará o local onde pode armazenar
as tabelas gerenciadas. Ao criar um metastore você precisa 
anexá-lo a um workspace.

* Para ter acesso ao console account é necessário ser um account admin. O que significa que a criação do workspace não é suficiente para dar a você o papel de Account Admin.

* Em resumo: O Administrador Global do Microsoft Entra ID torna-se o primeiro Account Admin do Databricks, mas essa associação não é automática para todos os Global Admins. Após a criação, o Global Admin pode (e deve) delegar a função de Account Admin do Databricks para o usuário final apropriado e, se quiser, remover a função de si mesmo.

Vamos lá

1. Crie um metastore(Esse metastore precisa está na mesma região que todas suas contas de armazenamento - ideal, workspace):
![alt text](UNITY_CATALOG_IMAGES/image-7-v.png)

![alt text](UNITY_CATALOG_IMAGES/image-v.png)

![alt text](UNITY_CATALOG_IMAGES/image-3-v.png)

* Você pode criar um metastore por região, não é possível criar vários metastores para uma única região.

* O parâmetro ADLS Gen2 path é opcional se não estiver forcendo o caminho neste momento, enquanto estiver criando
objetos como esquema, catálogos ou tabelas, você precisará fornecer o caminho ali. (Esse caminho serve para armazenar tabelas gerenciadas desse metastore específico)

2. Anexe ao Workspace

![alt text](UNITY_CATALOG_IMAGES/image-1-v.png)

# O Escopo da Conta Databricks (vs. Azure Subscription)

Antes do UC: O controle de acesso (ACLs, Contributor/Reader) era feito no nível da Assinatura Azure. Se você não estivesse na assinatura, não tinha acesso ao Workspace.

Com o UC: O Databricks Account Console e o Metastore operam no nível do Tenant (Locatário) do Microsoft Entra ID.

Um único Tenant do Azure pode ter várias Assinaturas e, consequentemente, vários Workspaces espalhados por essas assinaturas.

O Account Console do Databricks tem visibilidade de todos os Workspaces dentro desse Tenant (independentemente da Assinatura ou Grupo de Recursos em que foram criados).

Isso permite que você anexe Workspaces de diferentes Assinaturas a um único Metastore, centralizando o controle de acesso e os metadados para toda a organização (Tenant).

Em suma, o Unity Catalog oferece uma camada de governança universal que se estende por todos os seus Workspaces, elevando o gerenciamento do nível do Workspace/Assinatura para o nível da Conta Databricks (que corresponde ao Tenant do Azure).

![alt text](UNITY_CATALOG_IMAGES/image-4-v.png)

## Roles no Unity Catalog

![alt text](UNITY_CATALOG_IMAGES/image-6-v.png)

## Atribuindo permissões via Microsft Entra ID

1. Crie um usuário de exemplo para testar as permissões no UC.

![alt text](UNITY_CATALOG_IMAGES/image-8-v.png)

Usuário 1 - Jarvis (Workspace Admin) - Password: Mo@34633493 </br>
Usuário 2 - Steve (Developers) -  Password: Mo@34633493

2. Criado os usuários no Microsoft Entra ID é necessário adicioná-los no UC

![alt text](UNITY_CATALOG_IMAGES/image-9-v.png)

* Obs: Na prática real você fará isso por meio do provisionamento de usuários, em que você
adiciona o seu provedor de identidade e vai sincrizar com todos os seus usuários da organização.

![alt text](UNITY_CATALOG_IMAGES/image-10-v.png)

## User and Group Management

1. Adicionar os usuários no UC

![alt text](UNITY_CATALOG_IMAGES/image_k.png)

2. Criar grupos no UC
- Workspace Admins
- Developers

![alt text](UNITY_CATALOG_IMAGES/image-1_k.png)

3. Dar as roles necessárias para cada grupo
 - Wokspace Admin - Workspace Admins Group
 - Wokspace User - Developers Group

 ![alt text](UNITY_CATALOG_IMAGES/image-2_k.png)

 * Permissão no nível admin, permite gerenciar grupos de usuários e a configuração do workspace. A permissão no nível user dá a permissão do usuário apenas acessar o workspace, criar coisas como schema, tabelas e pode ser proprietário dessas tabelas.

 Como estamos utilizando a databricks e ele não e necessário um serviço da Azure, o usuário não precisa ter acesso a nenhuma assinatura, apenas o link do workspace.

 ![alt text](UNITY_CATALOG_IMAGES/image-3_k.png)

Se eu quiser desativar a criação de personal compute, para todos os usuários, faça isso:

![alt text](UNITY_CATALOG_IMAGES/image-4_k.png)

Agora o usuário Steve não consegue criar um personal compute.

## Cluster Policy

Cluster policy é util para controlar a capacidade dos usuários de configurar o cluster com base em determinadas regras. Portanto, essas regras especificam quais atributos ou valores de atributos podem ser usados durante a criação do cluster. E, com base nas políticas de cluster, ele têm algo como regras de controle de acesso que podem limitar os usuários e grupos específicos.

* Obs: Com a permissão de Workspace Admin eu posso liberar para um usuário específico conseguir criar um cluster.

![alt text](UNITY_CATALOG_IMAGES/image-5_k.png)

1. No console compute -> policies -> create policy -> Atribua um nome a policy -> Em familiy posso pegar modelos para me basear e criar a minha própria policy.

* Obs: O workspace admin vai poder criar cluster com base em várias policy, então quando quiser criar a policy para determinado usuário, crie um grupo sem ter a permissão de Admin e depois cria a policy para criação de cluster.

* Análise o terraform para verificar como criar policy por meio de IaC

# Cluster Pools

Podem ser utilizados para limitar a criação de usuários a um determinado tipo de instância ou para determinado grupo,
porém permite um tempo de inicialização mais rápido por conta que defino o mínimo instâncias igual a 1.

Então eu posso criar um pool com max instances = 2 e coloco min_idle = 1, porém nas permissões eu posso atribuir, por exemplo,
que meu grupo de developers acesse esse pool ai quando ele da um create cluster na seleção de cluster ele seleciona o nome do meu pool de forma que vai criar mais uma instância chegando ao máximo que é 2, porém o tempo de inicialização e bem rápido.

No cluster policy eu posso criar uma definição por exemplo para meus usuários criarem máquinas apenas do meu pool.

1. Copio o pool id

![alt text](UNITY_CATALOG_IMAGES/image-6_k.png)

2. Atribuo no value do Json

![alt text](UNITY_CATALOG_IMAGES/image-7_k.png)