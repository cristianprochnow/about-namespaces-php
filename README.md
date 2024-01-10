# Conflitos de importação

Um problema muito comum ao usar múltiplas importações no PHP é a de conflitos de nomes de função ou classes. Quando 2 ou mais arquivos são importados e ambos compartilham recursos que possuam o mesmo nome, esses entram em conflito.

Esse era um problema muito frequente que era resolvido de outras formas. Contudo, recursos como a implementação moderna de `namespaces` garante com que essa diferenciação seja feita de forma mais clara e limpa.

Então, quando faço algo como o código abaixo, sem usar o `namespace` em cada arquivo de classe, um erro ocorre no processo.

```php
require_once __DIR__ . '/src/WaysToMove/Audi/Vehicle.php';
require_once __DIR__ . '/src/WaysToMove/Honda/Vehicle.php';
```
```
Cannot declare class Vehicle, because the name is already in use
```

Esse erro pode ocorrer com qualquer outro recurso que seja, não apenas classes. Então, se em ambos os arquivos eu tiver funções, constantes, variáveis e qualquer outro recurso semelhante, ambos vão conflitar, resultando no erro em questão.

## Usando `namespace`

Já que o problema é esse, vamos então fazer um teste. Ao colocar um `namespace` na classe da Honda, e importar apenas esse arquivo, o seguinte é retornado.

```php
# Honda/Vehicle.php
namespace WaysToMove\Honda;

class Vehicle
{

}

# index.php
// require_once __DIR__ . '/src/WaysToMove/Audi/Vehicle.php';
require_once __DIR__ . '/src/WaysToMove/Honda/Vehicle.php';

$vehicle = new Vehicle();
```
```
Uncaught Error: Class "Vehicle" not found in
```

O erro acima é retornado pois o PHP primeiro está procurando essa classe no namespace global daquele contexto. E agora a gente definiu que a classe `Vehicle` está sim contida em um namespace definido. Com isso, para resolver, temos que declarar que queremos aquela classe, daquele namespace em questão.

```php
// require_once __DIR__ . '/src/WaysToMove/Audi/Vehicle.php';
require_once __DIR__ . '/src/WaysToMove/Honda/Vehicle.php';

$vehicle = new WaysToMove\Honda\Vehicle();



# ou posso também usar a keyword `use`



// require_once __DIR__ . '/src/WaysToMove/Audi/Vehicle.php';
require_once __DIR__ . '/src/WaysToMove/Honda/Vehicle.php';

use WaysToMove\Honda\Vehicle;

$vehicle = new Vehicle();
```

E, assim, o erro para de ocorrer. Então esse é o princípio básico de uso de `namespace` no PHP.

## Recomendações

### Nome do `namespace`

Você pode nomear isso da forma que quiser e organizar do seu modo. Contudo, é um costume da comunidade colocar no namespace a mesma estrutura que está sendo seguida nas pastas.

Sendo assim, geralmente em projetos open-source, é colocado primeiro o nome do pacote, e após isso coloca certinho a estrutura de pastas. Então vamos dizer que esse projeto possua um nome de `Movement`. Então, o namespace da classe `Vehicle` dentro da classe da Honda vai ser `Movement\WaysToMove\Honda`.

Desse modo, tudo fica mais organizado e mais intuitivo também. E as letras maiúsculas são incluídas nos nomes das pastas também, para seguir o padrão e tudo ficar no mesmo contexto.

### Importar em mesmos `namespace`

Vamos dizer que dentro da pasta da Honda, eu tenho mais uma classe, chamada `Motorcycle`. E o namespace é o mesmo da minha classe `Vehicle`. Sendo assim, para usar essa classe dentro da outra, não preciso importar ou usar algo como `use WaysToMove\Honda\Motorcycle`. Já que ambas fazem parte do mesmo namespace, posso simplesmente chamar a `Motorcycle` dentro da `Vehicle` que tudo vai funcionar conforme deveria.

### Uso de classes globais em namespace

Se você está dentro de um namespace específico, e pretende usar alguma classe que seja global, vai ter que colocar o `\` antes da classe, para indicar ao PHP que o que deseja está fora daquele namespace ao qual você está contido.

Então, vamos dizer que tendo de `Vehicle.php`, da pasta da Honda, eu quero usar a classe global do PHP `DateTime`, eu preciso usar o símbolo acima para indicar isso. Isto é:

```php
namespace WaysToMove\Honda;

# Vai dar erro.
$datetime = new DateTime();

# Vai dar certo.
$datetime = new \DateTime();



# Ou também, usar a kayword `use`.



use DateTime;

$datetime = new DateTime();
```

### Nome qualificado e não qualificado de namespace

Quando chamarmos uma classe, de outro namespace, e não colocarmos o `\` antes, isso está dizendo para o compilador buscar essa mesma classe dentro do namespace que estamos mexendo.

Então, quando queremos usar algo que está em outro namespace, fora do que estamos mexendo, sempre tem que indicar com a `\` no início, para dizer que é pra buscar da raiz do contexto global, assim copmo funciona a busca por arquivos nos gerenciadores de arquivos.

```php
# Vai buscar a partir daquele namespace.
$class = new Notification\Email();

# Vai buscar a partir do contexto global, da raiz.
$class = new \Notification\Email();
```

Sendo assim, há **3 categorias de qualificação do nome de namespace**:

```php
# Nome não qualificado
$class = new Email();

# Nome qualificado
$class = new Notification\Email();

# Nome totalmente qualificado
$class = new \Notification\Email();
```

### Conflito de nomes

No exemplo principal, ao importar ambas classes, podemos sofrer com um problema de conflitos de nome. Em ambos os namespaces, há a mesma classe `Vehicle`. Com isso, para resolver isso usaremos os apelidos (`aliases`).

Sendo assim, mesmo que os nomes das classes seja iguais para ambos os contextos, ainda poderemos diferenciar. Isso vale também para nomes de pacotes que estejamos usando. Pois, vamos dizer que importamos um pacote que possui o mesmo nome da classe que estamos criando. Basta, então, colocar um apelido nesse pacote e tudo vai funcionar bem.

```php
# Vai dar conflito.
use \WaysToMove\Honda\Vehicle;
use \WaysToMove\Audi\Vehicle;

$hondaVehicle = new Vehicle();
$audiVehicle = new Vehicle();



# Então podemos apelidar pelo menos uma delas, ou quantas quisermos.
use \WaysToMove\Honda\Vehicle as HondaVehicle;
use \WaysToMove\Audi\Vehicle as AudiVehicle;

$hondaVehicle = new HondaVehicle();
$audiVehicle = new AudiVehicle();
```

### Importar vários

Caso um namespace possua várias classes e no arquivo em questão precisemos usar tudo isso, podemos fazer um único `use`, apontando em uma linha apenas tudo que desejamos.

```php
# Em várias linhas.
use \WaysToMove\Honda\Vehicle;
use \WaysToMove\Honda\Motorcycle;

# Em uma linha apenas.
use \WaysToMove\Honda\{Vehicle, Motorcycle};

# Ou também importando o namespace.
use \WaysToMove\Honda;

new Honda\Vehicle();
new Honda\Motorcycle();
```
