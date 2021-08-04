Todo este artigo é baseado no curso [Flutter: App Android e iOS nativo, SQLite, Biometria, Câmera, GPS e Google Maps](https://balta.io/cursos/flutter-android-ios-app-nativo-sqlite-biometria-camera-gps-google-maps) onde implementamos na prática o SQLite em um App nativo completo para Android e iOS, compartilhando o mesmo codebase.

## Flutter SQLite e banco de dados local

O SQLite com certeza é um dos bancos locais mais populares do mercado, e isto significa que sua adoção e as chances de você precisar trabalhar com ele em algum projeto são grandes.

Além disso o uso do SQLite no Flutter é relativamente simples e nativamente ele já nos provê diversos recursos como proteção contra SQL Injection e até mesmo a dispensa na escrita de código SQL.

Vou deixar aqui também um vídeo onde falo um pouco mais sobre **SQL Injection** um assunto batido há muito tempo mas que ainda traz dor de cabeça em diversos cenários.

[![IMAGE ALT TEXT HERE](http://img.youtube.com/vi/6mdIE0c3K_4/0.jpg)](http://www.youtube.com/watch?v=6mdIE0c3K_4)

Além do Flutter SQLite havia também o Hive, que é um banco de dados local, NoSQL, escrito em Dart! Parece o casamento perfeito, porém existem alguns riscos que não gostaria de correr neste momento, além dele ser menos popular que o SQLite.

## Dependências

Para trabalhar com Flutter SQLite precisamos utilizar o pacote `sqflite`, que nos irá prover tudo que precisamos a nível de banco de dados.

Como o SQLite é um banco de dados local, vamos precisar salvar ele no dispositivo, então precisamos de dois pacotes adicionais, o `path` e `path_provider`.

Desta forma, crie uma nova aplicação com Flutter e adicione os seguintes pacotes no pubspec.yaml.

```
path:
path_provider:
sqflite:
```

## Script de criação do banco

O primeiro passo para começar com Flutter SQLite é criar o banco de dados, e neste momento não temos como fugir da escrita de código SQL.

Como o foco deste artigo não é a linguagem SQL, vou deixar o código que utilizei para criar o banco de dados no curso 7201.

```sql
CREATE TABLE contacts(id INTEGER PRIMARY KEY,name TEXT, email TEXT, phone TEXT,image TEXT, addressLine1 TEXT, addressLine2 TEXT, latLng TEXT)
```

Em adicional, eu armazeno sempre estes códigos SQL em constantes no código, evitando mudanças em tempo de execução e possíveis erros de digitação.

Para este caso em específico, eu criei uma constante chamada `CREATE_CONTACTS_TABLE_SCRIPT` com o conteúdo como `String`.

```dart
const String CREATE_CONTACTS_TABLE_SCRIPT = "CREATE TABLE contacts(id INTEGER PRIMARY KEY,name TEXT, email TEXT, phone TEXT,image TEXT, addressLine1 TEXT, addressLine2 TEXT, latLng TEXT)";
```

## Como funciona?

No SQLite não ficamos conectados ao banco o tempo todo, ao invés disso, abrimos o banco (Nos conectamos), executamos nossas consultas e depois fechamos tudo.

Desta forma, sempre que formos executar algo no Flutter SQLite precisamos da função `openDatabase` para nos conectar (Abrir) o banco antes.

Para facilitar, minha sugestão é criar um método que retorna o banco de dados, se possível aplicando o Design Patter - Repository Pattern para abstrair este acesso à dados das interfaces.

Neste caso, vou criar um método privado, assíncrono chamado `_getDatabase()` que nos retorna o banco de dados criado.

Caso este banco não exista, vamos informar ao SQLite que o mesmo deve ser criado, utilizando o Script que definimos anteriormente.

Note que a criação ou abertura do banco é um processo que pode demorar, sendo assim ele é um `Future`.

```dart
Future<Database> _getDatabase() async {
  return openDatabase(
    join(await getDatabasesPath(), DATABASE_NAME),
    onCreate: (db, version) {
      return db.execute(CREATE_CONTACTS_TABLE_SCRIPT);
    },
    version: 1,
  );
}
```

O método `getDatabasesPath` recuperar o caminho padrão para o banco de dados e caso o mesmo não seja encontrado o método `onCreate` será disparado.

Dentro deste método `onCreate` temos a execução do nosso Script SQL que ficou armazenado em uma constante chamada `CREATE_CONTACTS_TABLE_SCRIPT`.

Em adicional, recomendo armazenar também o nome do banco e das tabelas em constante, como fiz com o `DATABASE_NAME`.

## Operações básicas

Com o banco de dados aberto, podemos executar as operações básicas que chamamos de **CRUD**, a sigla para Create (Criar), Read (Ler), Update (Atualizar) e Delete (Excluir).

### Preparando o modelo

Antes de prosseguir precisamos de um modelo de dados, e isto pode (deve) ser feito pelos nossos **models** na aplicação.

O `sqflite` sempre receberá e retornará um `Map<String, dynamic>` então é uma boa ideia deixar seu modelo já preparado para ser convertido de/para esta estrutura.

```dart
class ContactModel {
  int id = 0;
  String name = "";
  String email = "";
  String phone = "";
  String image = "assets/images/profile-picture.png";
  String addressLine1 = "";
  String addressLine2 = "";
  String latLng = "";

  ContactModel({
    this.id,
    this.name,
    this.email,
    this.phone,
    this.image,
    this.addressLine1,
    this.addressLine2,
    this.latLng,
  });

  Map<String, dynamic> toMap() {
    return {
      'id': id,
      'name': name,
      'phone': phone,
      'email': email,
      'image': image,
      'addressLine1': addressLine1,
      'addressLine2': addressLine2,
      'latLng': latLng,
    };
  }
}
```

### Salvando um registro

No `sqflite` a inserção de um novo registro é dado pelo método `db.insert` onde precisamos informar a tabela e o item que desejamos incluir.

Lembre-se que este item que precisamos enviar tem que estar no formato `Map<String, dynamic>` então podemos converter nosso modelo para este utilizando o método `toMap()` criado anteriormente.

Para exemplificar este uso, criei um método chamado `create` onde recebemos um modelo e inserimos ele no banco de dados.

```dart
Future create(ContactModel model) async {
    try {
      final Database db = await _getDatabase();

      await db.insert(
        TABLE_NAME,
        model.toMap(),
      );
    } catch (ex) {
      print(ex);
      return;
    }
  }
```

Note que executamos tudo dentro de um Try/Catch pois podem haver erros de execução e/ou disco aqui. É bom sempre ficar prevenido.

A primeira coisa que precisamos fazer é recuperar nosso banco, e isto é feito utilizando o método criado previamente, utilizando `await _getDatabase()`.

Em seguida temos a execução do `db.insert` persistindo o modelo. Note que novamente utilizamos uma constante chamada `TABLE_NAME` para definir o nome da tabela.

### Lendo os registros

Para leitura podemos utilizar o método `query` enviando como parâmetro o nome da tabela que queremos buscar os resultados.

Este método retornará um `List<Map<String, dynamic>>` e embora você possa devolver esta mesma lista para sua interface, é comum retornarmos uma lista tipada ao invés de dinâmica, ou seja um `List<ContactModel>`.

Desta forma, temos o método `getContacts()` que faz a busca dos contatos, converte para uma lista tipada e retorna o resultado.

```dart
Future<List<ContactModel>> getContacts() async {
  try {
    final Database db = await _getDatabase();
    final List<Map<String, dynamic>> maps = await db.query(TABLE_NAME);

    return List.generate(
      maps.length,
      (i) {
        return ContactModel.fromMap(maps[i]);
      },
    );
  } catch (ex) {
    print(ex);
    return new List<ContactModel>();
  }
}
```

Note que utilizamos o `List.generate` para iterar pela lista tendo como resultado uma nova, do tipo `List<ContactModel>`.

### Update e Delete

Neste momento você já deve ter pego a ideia. Basicamente abrimos o banco e pedimos para uma instrução ser executada, baseada em nosso modelo.

Para os métodos **update** e **delete** precisamos enviar um parâmetro adicional, que é o **ID** do contato, e isto pode ser feito através da propriedade `where` e `whereArgs`.

```dart
await db.update(
  TABLE_NAME,
  model.toMap(),
  where: "id = ?",
  whereArgs: [model.id],
);
```

Na propriedade `where` escrevemos nosso SQL, utilizando um **?** onde desejamos que haja um parâmetro. Posteriormente o `whereArgs` lerá este SQL e substituirá as **?** pelos parâmetros informados nele.

É importante lembrar que esta substituição ocorrerá na ordem, ou seja, a primeira **?** será substituída pelo primeiro parâmetro, e assim por diante.

Nosso último método, o **delete** precisa chamar a função `db.delete()` assim como fizemos no **update**, inclusive informando o **ID**.

```dart
await db.delete(
  TABLE_NAME,
  where: "id = ?",
  whereArgs: [id],
);
```

## Código Fonte

O código deste artigo se baseia no projeto que realizamos no curso [Flutter: App Android e iOS nativo, SQLite, Biometria, Câmera, GPS e Google Maps](https://balta.io/cursos/flutter-android-ios-app-nativo-sqlite-biometria-camera-gps-google-maps) e está disponível [neste repositório do GitHub](https://github.com/balta-io/7201).

## Conteúdo adicional

Se você se interessou por este conteúdo, talvez valha a pena dar uma olhada neste bate papo que fizemos sobre o Flutter, onde falamos de muitos pontos interessantes.

[![Bate Papo](http://img.youtube.com/vi/aXHI-b4zmmw/0.jpg)](http://www.youtube.com/watch?v=aXHI-b4zmmw)


<div role="main" id="blog-s1-flutter-15c15f0b7777b4675fad"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-flutter-15c15f0b7777b4675fad', 'UA-48664517-12').createForm();</script>
