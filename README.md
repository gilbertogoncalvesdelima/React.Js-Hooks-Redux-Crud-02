Link Projeto:

```js
https://react-js-hooks-redux-crud-02-gilberto-goncalves-de-lima.netlify.app/
```
Para gerar o build, do projeto para a plataforma netlify

```js
npm run build
```

## Criação de um CRUD no React, Hooks

Neste tutorial, construiremos um aplicativo da web de criação, leitura, atualização e exclusão com React usando React Hooks. Os ganchos nos permitem usar o estado e outros recursos em componentes funcionais em vez de escrever componentes de classe.

Este tutorial está dividido nas seguintes seções:

1 - Configurando o projeto
2- Adicionando tabela de usuários
3 - Adicionar um usuário
4 - Excluindo um usuário
5 - Atualizar um usuário
6 - Usando o Gancho de Efeito
7 - Buscar usuários de uma API

## Configurando o projeto

Começaremos:

Canal do youtube:

```js
npx create-react-app react-crud
```

Em seguida, navegue até esta pasta e exclua tudo da pasta / src, exceto App.js, index.js e index.css

Para index.css, usaremos um padrão CSS simples chamado Skeleton, que você pode encontrar aqui: http://getskeleton.com/

Adicione os estilos na pasta / public em index.html:

```js
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/skeleton/2.0.4/skeleton.min.css">
```
Em seguida, converta App.js em um componente funcional e adicione a seguinte configuração. Observe como o esqueleto da placa de caldeira CSS funciona facilmente

```js
import React from 'react'

const App = () => {

  return (
    <div className="container">
      <h1>React CRUD App with Hooks</h1>
      <div className="row">
        <div className="five columns">
          <h2>Add user</h2>
        </div>
        <div className="seven columns">
          <h2>View users</h2>
        </div>
      </div>
    </div>
  )
}

export default App
```
## Adicionar tabela de usuários

Recuperaremos nossos dados de usuário de um arquivo separado. Vamos criar data.js dentro de / src e adicionar uma matriz chamada users com alguns objetos de usuário dentro e, em seguida, exportá-la:

```js
const userList = [
    {
        id: 1,
        name: 'Frank',
        username: 'Frank Degrassi'
    },
    {
        id: 2,
        name: 'Birgit',
        username: 'Birgit Boswald'
    }
];

export default userList;
```

Em seguida, crie uma pasta chamada / tables e adicione um arquivo UserTable.jsx. Aqui, adicionaremos uma tabela básica que percorre os usuários. Observe que estamos usando um operador ternário que é o mesmo que uma instrução if / else que retorna imediatamente. Além disso, estamos desestruturando as propriedades do objeto para que não tenhamos que reescrever a propriedade novamente. Se não houver usuários encontrados, mostraremos uma célula vazia com algum texto.


```js
import React from 'react';

const UserTable = (props) => {
    return (
        <table>
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Username</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody>
                { props.users.length > 0 ? (
                    props.users.map(user => {
                        const {id, name, username} = user;
                        return (
                            <tr>
                                <td>{id}</td>
                                <td>{name}</td>
                                <td>{username}</td>
                                <td>
                                    <button>Delete</button>
                                    <button>Edit</button>
                                </td>
                            </tr>
                        )
                    })
                ) : (
                    <tr>
                        <td colSpan={4}>No users found</td>
                    </tr>
                )   
                }
            </tbody>
        </table>
    )
}

export default UserTable;
```

A tabela faz um loop sobre os usuários recebidos pelo App.js por meio dos props do usuário. Vamos adicioná-los ao App.js e também à funcionalidade para recuperar usuários de data.js, o que faremos com useState. Cada useState tem um getter e um setter.

```js
import React, {useState} from 'react'
import userList from './data.js';
import UserTable from './tables/UserTable';

const App = () => {

  const [users, setUsers] = useState(userList);

  return (
    <div className="container">
      <h1>React CRUD App with Hooks</h1>
      <div className="row">
        <div className="six columns">
          <h2>Add user</h2>
        </div>
        <div className="six columns">
          <h2>View users</h2>
          <UserTable users={users} />
        </div>
      </div>
    </div>
  )
}

export default App
```
Certifique-se de importar a UserTable em App.js e adicionar os usuários como adereços em UserTable.

## Adicionar um usuário

Em seguida, adicionaremos a funcionalidade para adicionar um usuário, primeiro adicionando a função em App.js que recebe o novo usuário do componente Adicionar usuário que criaremos.

A função addUser coloca um objeto contendo um novo usuário em nossa matriz de objetos de usuário de usuários. Fazemos isso usando nossos setUsers da função useState. Usando o operador spread, mantemos a matriz do usuário atual igual. O ID que definiremos apenas com base na quantidade atual de usuários mais um.

```js
const addUser = user => {
    user.id = users.length + 1;
    setUsers([...users, user]);
  }
```

Em seguida, passaremos esta função para o nosso componente Adicionar usuário:

```js
<AddUserForm addUser={addUser} />
```
Que iremos criar agora! Crie uma pasta / formulários com um arquivo chamado AddUserForm.jsx.

```js
import React, {useState} from 'react';

const AddUserForm = (props) => {

    const initUser = {id: null, name: '', username: ''};

    const [user, setUser] = useState(initUser);

    return (
        <form>
            <label>Name</label>
            <input className="u-full-width" type="text" name=”name” value={user.name} />
            <label>Username</label>
            <input className="u-full-width" type="text" name=”username” value={user.username} />
            <button className="button-primary" type="submit">Add user</button>
        </form>
    )
}

export default AddUserForm;
```
Novamente, estamos usando useState para gerenciar o estado de nosso novo usuário. O estado inicial dos valores do usuário estão vazios. Agora vamos adicionar as funções onChange e onSubmit. Para handleChange, nós desestruturamos as propriedades do objeto event.target. Em seguida, definimos dinamicamente nossas chaves de objeto com base no campo de entrada usado:

```js
import React, {useState} from 'react';

const AddUserForm = (props) => {

    const initUser = {id: null, name: '', username: ''};

    const [user, setUser] = useState(initUser);

    const handleChange = e => {
        const {name, value} = e.target;
        setUser({...user, [name]: value});
    }

    const handleSubmit = e => {
        e.preventDefault();
        if (user.name && user.username) {
           handleChange(e, props.addUser(user));
        }
    }

    return (
        <form>
            <label>Name</label>
            <input className="u-full-width" type="text" value={user.name} name="name" onChange={handleChange} />
            <label>Username</label>
            <input className="u-full-width" type="text" value={user.username} name="username" onChange={handleChange} />
            <button className="button-primary" type="submit" onClick={handleSubmit} >Add user</button>
        </form>
    )
}

export default AddUserForm;
```
Excelente! Agora podemos adicionar um usuário. Observe em nosso handleSubmit que estamos impedindo a atualização da página padrão e também verificando se nosso user.name e user.username realmente foram preenchidos.

Atualizar: para garantir que nosso novo usuário só seja adicionado quando o estado tiver sido definido para esse novo usuário, passamos a addUserfunção também um retorno de chamada após o término de handleChange. Isso resolve o bug se você adicionar o mesmo usuário rapidamente após o outro.

## Excluindo um usuário

Agora vamos adicionar a funcionalidade de excluir um usuário, o que é bastante simples. Vamos apenas filtrar nossa matriz de usuários e filtrar o usuário que possui o ID do usuário que queremos excluir. Novamente, usaremos nossa função setUsers para atualizar o estado dos novos usuários.

```js
<button onClick={() => props.deleteUser(id)}>Delete</button>
```
App.js

```js
const deleteUser = id => setUsers(users.filter(user => user.id !== id));

<UserTable users={users} deleteUser={deleteUser} />
```

## Atualizar um usuário

Atualizar um usuário é um pouco mais difícil do que adicionar ou excluir um usuário. Primeiro, vamos configurar o formulário em ./forms/EditUserForm.jsx e importá-lo para App.js. Vamos apenas copiar nosso AddUserForm.jsx e mudar o currentUser para o usuário que estamos recebendo do App.js:

```js
import React, {useState} from 'react';

const EditUserForm = (props) => {

    const [user, setUser] = useState(props.currentUser);

    const handleChange = e => {
        const {name, value} = e.target;
        setUser({...user, [name]: value});
    }

    const handleSubmit = e => {
        e.preventDefault();
        if (user.name && user.username) props.updateUser(user);
    }

    return (
        <form>
            <label>Name</label>
            <input className="u-full-width" type="text" value={user.name} name="name" onChange={handleChange} />
            <label>Username</label>
            <input className="u-full-width" type="text" value={user.username} name="username" onChange={handleChange} />
            <button className="button-primary" type="submit" onClick={handleSubmit} >Edit user</button>
            <button type="submit" onClick={() => props.setEditing(false)} >Cancel</button>
        </form>
    )
}
```

onSubmit, enviamos os usuários atualizados de volta para App.js

No App.js, usaremos a função useState novamente para verificar se o usuário está editando e para decidir qual usuário está sendo editado:


```js
const [editing, setEditing] = useState(false);

const initialUser = {id: null, name: '', username: ''};

const [currentUser, setCurrentUser] = useState(initialUser);
```

Mostraremos o formulário AddUser ou EditUser com base no estado de edição:

```js
<div className="container">
      <h1>React CRUD App with Hooks</h1>
      <div className="row">
        <div className="five columns">
          { editing ? (
            <div>
              <h2>Edit user</h2>
              <EditUserForm 
                currentUser={currentUser}
                setEditing={setEditing}
                updateUser={updateUser}
              />
            </div>
          ) : (
            <div>
              <h2>Add user</h2>
              <AddUserForm addUser={addUser} />
            </div>
          )}
        </div>
        <div className="seven columns">
          <h2>View users</h2>
          <UserTable users={users} deleteUser={deleteUser} editUser={editUser} />
        </div>
      </div>
    </div>
```

Em seguida, adicionaremos nossas funções editUser e updateUser em App.js:

```js
const editUser = (id, user) => {
  setEditing(true);
  setCurrentUser(user);
}
const updateUser = (newUser) => {
  setUsers(users.map(user => (user.id === currentUser.id ? newUser : user)))
}
```
Excelente! Agora podemos editar nossos usuários. Vamos corrigir o último problema na próxima seção.

## Usando o Gancho de Efeito

Atualmente não é possível alterar o usuário durante a edição, podemos consertar isso usando o gancho de efeito. Isso é semelhante a componentDidMount () em componentes de classe. Primeiro, certifique-se de importar useEffect em EditUserForm.jsx

```js
useEffect(() => {
    setUser(props.currentUser)
}, [props])
```

Isso fará com que, quando o componente for renderizado novamente, os adereços também serão atualizados.

Super! Concluímos a construção de nosso aplicativo React CRUD com Ganchos.

## Buscar usuários de uma API

Atualmente, temos nossos dados armazenados em um arquivo JS simples, mas na maioria dos casos você deseja obter seus dados de uma fonte externa / API. Nesta seção de bônus, construiremos uma função para buscar a fonte de dados de forma assíncrona.

Vamos usar esta API gratuita para buscar três usuários aleatórios:
https://randomuser.me/api/?results=3

Buscar dados assíncronos é bastante simples e podemos usar várias soluções para isso, por exemplo:

Usando uma biblioteca como axios
Usando promessas
Usando async / await (estilo mais limpo de promessas por escrito).
Eu gosto de usar o método de espera assíncrono. É assim que parece:


```js
const fetchData = async (amount) => {
 const response = await fetch(`https://randomuser.me/api/?results=${amount}`);
 const json = await response.json();
 console.log(json);
}
```
Nós apenas colocamos asyncna frente de nossa função e então podemos usar awaitpara executar as próximas linhas de código apenas quando essa linha for concluída. Convertemos o resultado em JSON e, em seguida, registramos os resultados na tela. useEffectColocaríamos isso em nosso gancho do App.js e buscaríamos os dados na montagem do componente, mas vamos dar um passo adiante.

Criaremos nosso próprio React Hook personalizado, colocando o código acima em um arquivo separado e, em seguida, retornando o resultado e o estado de carregamento.

Crie uma nova pasta chamada hookscom um arquivo useAsyncRequest.jscom o seguinte código:

```js
import {useState, useEffect} from 'react';

const useAsyncRequest = amount => {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(false);

    useEffect(() => {
        const fetchData = async () => {
            try {
                setLoading(true);
                const response = await fetch(`https://randomuser.me/api/?results=${amount}`);
                const json = await response.json();
                setData(json.results, setLoading(false));
            } catch (err) {
                console.warn("Something went wrong fetching the API...", err);
                setLoading(false);
            }
        }

        if (amount) {
         fetchData(amount);
        }
    }, [amount]);

    return [data, loading]
}

export default useAsyncRequest;
```

O que acontece aqui é o seguinte. Com o useEffectgancho, buscamos dados da API no carregamento da página. Esta função será acionada sempre que nosso valor for alterado, portanto, apenas uma vez, porque nosso valor será um número fixo (3 no meu exemplo).

Eu adicionei um bloco try-catch para adicionar tratamento de erros para a solicitação de espera assíncrona. Em seguida, retornaremos duas variáveis ​​de estado: dados e carregamento. Usaremos em nosso componente de aplicativo.

Importe este arquivo dentro do componente App e adicione o seguinte:



```js
const [data, loading] = useAsyncRequest(3);
  const [users, setUsers] = useState(null);

  useEffect(() => {
    if (data) {
      const formattedUsers = data.map((obj, i) => {
        return {
          id: i,
          name: obj.name.first,
          username: obj.name.first + " " + obj.name.last,
        };
      });
      setUsers(formattedUsers);
    }
  }, [data]);
```
O que mudou aqui é que os usuários ou agora são definidos como null padrão, e assim que nosso Gancho nos devolver o resultado, definiremos os usuários como os usuários buscados.

Os dados que recebemos não correspondem ao nosso componente userTable, portanto, temos que formatar o resultado. Estou fazendo isso aqui mapeando a matriz e para cada objeto que retorna um novo objeto que podemos usar em nosso aplicativo.

A useEffectfunção / gancho é acionada toda vez que nossa datavariável muda. Então, basicamente, sempre que nosso gancho useAsyncRequest está pronto para buscar os dados. Legal certo!

Por fim, atualizaremos nosso componente App para que ele apenas renderize a tabela do usuário quando não estiver carregando e houver realmente usuários:

```js
{loading || !users ? (
          <p>Loading...</p>
        ) : (
          <div className="seven columns">
            <h2>View users</h2>

            <UserTable
              users={users}
              deleteUser={deleteUser}
              editUser={editUser}
            />
          </div>
        )}
```