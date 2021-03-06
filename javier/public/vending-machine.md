# Máquina expendedora

Script para gestionar por consola una máquina expendedora

**Características:**

* Solo los usuarios registrados pueden usar la máquina
* El sistema funciona sin dinero real, en su lugar se usa un sistema de puntos.
* Los usuarios disponen de unos puntos para gastar en la máquina que el departamento de personal asigna.
* La maquina no concederá creditos, solo trabaja en base a los puntos disponibles.
* Para agilizar la reposición de lo consumido... la máquina debe controlar el stock.
* El usuario administrador debe poder gestionar productos (añadir, eliminar, control de precios y stock...) y también usuarios (alta, baja, puntos de usuaios...)

```javascript

var users = [];
var products = [];

var vendingMachine = {
    users : [
        { id: 0, username: 'jgarcia', password: 'zzz', role: 'admin', wallet: 100 },
        { id: 1, username: 'mrRobot', password: 'aaa', role:'client', wallet: 100 },
        { id: 2, username: 'fSociety', password: 'bbb', role: 'client', wallet: 100 },
        { id: 3, username: 'neo', password: 'ccc', role: 'client', wallet: 100 }
    ],
    products : [
        { id: 1, name: 'Pepsi', stock: 20, max_units: 30, price: 10},
        { id: 2, name: 'Agua', stock: 10, max_units: 15, price: 8},
        { id: 3, name: 'Doritos', stock: 5, max_units: 10, price: 10},
        { id: 4, name: 'Twix', stock: 10, max_units: 15, price: 12},
        { id: 5, name: 'M&M\'s', stock: 10, max_units: 20, price: 15},
        { id: 6, name: 'Skittles', stock: 10, max_units: 20, price: 10}
    ],
    actions: {
        login: function(user, pass){
            var success = false,
                userData = '',
                status = 0;

            for (var i = 0; i < vendingMachine.users.length; i++){
                if(user === vendingMachine.users[i].username && pass === vendingMachine.users[i].password){
                    success = true;
                    userData = vendingMachine.users[i];
                    status = 1;
                    userData.status=status;
                    break;
                }
            }

            return userData;
        },
        checkUser: function (user){
            var match = false,
                position = '',
                matchInfo = {};

            console.groupCollapsed('Comparación nuevo usuario '+user+' con lista de usuarios.');
            for (var i = 0; i < vendingMachine.users.length; i++){
                console.log(user+' === '+vendingMachine.users[i].username+'?');
                if (vendingMachine.users[i].username == user) {
                    match = true;
                    position = i;
                    break;
                }
            }
            console.info(user+' existe = '+match);
            console.groupEnd();

            matchInfo = {match: match, position: position};

            return matchInfo;                        
        },
        addUser: function (loginName, loginPass, newUsername, newPass, newRole, newWallet){
            var checkUser = vendingMachine.actions.checkUser(newUsername),
                newUser = {},
                id = vendingMachine.users.length,
                msg = '';

            for (var i = 0; i < vendingMachine.users.length; i++){
                if( (loginName === vendingMachine.users[i].username) && (loginPass === vendingMachine.users[i].password) && (vendingMachine.users[i].role === 'admin') ){
                    console.log('Hola admin. Vamos a comprobar si existe '+newUsername+' antes de añadirlo a la lista.');

                    if ( checkUser.match === true ){
                        msg = '¡Usuario '+newUsername+' no añadido! '+newUsername+', ya existe!';
                    }else{
                        vendingMachine.users.push({ id: id, username: newUsername, password: newPass, role: newRole, wallet: newWallet});
                        msg = 'Usuario '+newUsername+' añadido ;)';                        
                    }
                    break;
                }
                else{
                    console.log("Login incorrecto.");
                }
            }

            return msg;
        },
        deleteUser: function (loginName, loginPass, user){
            var checkUser = vendingMachine.actions.checkUser(user),
                msg = '';

            for (var i = 0; i < vendingMachine.users.length; i++){
                if( (loginName === vendingMachine.users[i].username) && (loginPass === vendingMachine.users[i].password) && (vendingMachine.users[i].role === 'admin') ){
                    console.log('Hola admin. Vamos a comprobar si existe '+user+' antes de eliminarlo de la lista.');
                    if ( checkUser.match === true ){
                        vendingMachine.users.splice(checkUser.position,1);
                        msg = 'Usuario '+user+' eliminado';
                    }
                    else{
                        msg = 'El usuario introducido no existe';
                    }                    
                    break;
                }
                else{
                    console.log("Login incorrecto.");
                }
            }

            return msg;
        },    
        addPoints: function (loginName, loginPass, user, addPoints){
            var checkUser = vendingMachine.actions.checkUser(user),
                wallet = '',
                idUser = '',
                msg = '';

            for (var i = 0; i < vendingMachine.users.length; i++){
                if( (loginName === vendingMachine.users[i].username) && (loginPass === vendingMachine.users[i].password) && (vendingMachine.users[i].role === 'admin') ){
                    console.log('Hola admin. Se van a añadir '+addPoints+' puntos al usuario '+user);
                    if ( checkUser.match === true ){
                        idUser = checkUser.position;
                        wallet = vendingMachine.users[idUser].wallet+addPoints;
                        msg = 'Añadidos '+addPoints+' al usuario '+user+'. Total puntos en cartera: '+wallet;
                    }
                    else{
                        msg = 'El usuario introducido no existe';
                    }                    
                    break;
                }
                else{
                    console.log("Login incorrecto.");
                }
            }

            return msg;

        },
        subtractPoints: function (loginName, loginPass, user, subtractPoints){
            var checkUser = vendingMachine.actions.checkUser(user),
                wallet = '',
                idUser = '',
                msg = '';

            for (var i = 0; i < vendingMachine.users.length; i++){
                if( (loginName === vendingMachine.users[i].username) && (loginPass === vendingMachine.users[i].password) && (vendingMachine.users[i].role === 'admin') ){
                    console.log('Hola admin. Se van a restar '+subtractPoints+' puntos al usuario '+user);
                    if ( checkUser.match === true ){
                        idUser = checkUser.position;
                        wallet = vendingMachine.users[idUser].wallet+subtractPoints;
                        msg = 'Restados '+subtractPoints+' al usuario '+user+'. Total puntos en cartera: '+wallet;
                    }
                    else{
                        msg = 'El usuario introducido no existe';
                    }                    
                    break;
                }
                else{
                    console.log("Login incorrecto.");
                }
            }

            return msg;
        },
        resetPoints: function (loginName, loginPass, user){
            var checkUser = vendingMachine.actions.checkUser(user),
                wallet = '',
                idUser = '',
                msg = '';

            for (var i = 0; i < vendingMachine.users.length; i++){
                if( (loginName === vendingMachine.users[i].username) && (loginPass === vendingMachine.users[i].password) && (vendingMachine.users[i].role === 'admin') ){
                    if ( checkUser.match === true ){
                        console.log('Hola admin. Se van a eliminat todos los puntos ('+vendingMachine.users[checkUser.position].wallet+') de la cartera del usuario '+user);
                        idUser = checkUser.position;
                        wallet = 0;
                        vendingMachine.users[checkUser.position].wallet = wallet;
                        msg = 'Cartera del usuario '+user+' vaciada.';
                    }
                    else{
                        msg = 'El usuario introducido no existe';
                    }                    
                    break;
                }
                else{
                    console.log("Login incorrecto.");
                }
            }

            return msg;
        },
        userBalance: function(user, pass){
            var verifyUser = vendingMachine.actions.login(user, pass),
                wallet = 0,
                msg = '';

            if ( typeof verifyUser !== 'undefined'){
                console.log('Cuenta verificada con éxito.');  
                var userData = vendingMachine.actions.login(user, pass),
                    wallet = userData.wallet;

                switch(wallet) {   
                    case (wallet < 0):
                        msg = 'Error.';
                        break;                                   
                    case 0:
                        msg = 'Habla con el administrador para que te recargue más puntos.';
                        break;
                    case (wallet <= 10):
                        msg = 'Deberías de ir pensando en recargar tus puntos antes de que sea tarde.';
                        break;
                    case (wallet >= 10):                            
                        msg = 'Tienes puntos suficientes para seguir comprando en la máquina.';   
                        break;                                                                                
                }

                console.log(user+', tu saldo es de '+wallet+' puntos.' + msg);
            }else{
                console.log('Datos incorrectos, debes introducir tu clave para ver tu saldo.');
            }

            return wallet;
        },
        checkProduct: function(idproduct){
            var idproduct = idproduct,
                match = false,
                position = '',
                stock = '',
                price = '',
                productName = '';
                matchInfo = {};

            for (var i = 0; i < vendingMachine.products.length; i++){

                if ( vendingMachine.products[i].id === idproduct ){
                    match = true;
                    position = i;
                    productName = vendingMachine.products[i].name,
                    stock = vendingMachine.products[i].stock,
                    price = vendingMachine.products[i].price;

                    break;                   
                }
            }

            matchInfo = {match: match, name: productName, position: position, stock: stock, price: price};

            return matchInfo; 
        },
        addProduct: function(adminUser, adminPass, productName, productStock, productMaxUnits, productPrice){
            var login = vendingMachine.actions.login(adminUser,adminPass);
            if ( login.role === 'admin' ){

                var machineSlot = '',
                    newProduct = {},
                    msg = '';

                for (var i = 0; i < vendingMachine.products.length; i++) {
                    if (vendingMachine.products[i] === undefined) {
                        machineSlot = i;
                    }else{
                        machineSlot = vendingMachine.products.length+1;
                    }
                }


                for (var i = 0; i < vendingMachine.products.length; i++){  
                    newProduct = { id: machineSlot, name: productName, stock: productStock, max_unit: productMaxUnits, price: productPrice};
                    vendingMachine.products.splice(machineSlot, 0, newProduct);
                    msg = 'Producto '+productName+' añadido.';                        
                    break;
                }

                return msg;


            }else{
                console.log("No eres admin.");
            }

        },
        buyProduct: function (loginName, loginPass, idproduct){
            var getProduct = vendingMachine.actions.checkProduct(idproduct),
                wallet = '',
                updateWallet =' ',
                productName = '',
                stock = '',
                price = '',
                msg = '';

            for (var i = 0; i < vendingMachine.users.length; i++){
                if( (loginName === vendingMachine.users[i].username) && (loginPass === vendingMachine.users[i].password) ){
                    console.log('Login correcto. Hola, '+loginName);

                    wallet = vendingMachine.users[i].wallet;
                    console.log('Puntos disponibles: '+wallet);

                    for (var i = 0; i < vendingMachine.products.length; i++){

                        if ( vendingMachine.products[i].id === idproduct ){
                            
                            productName = vendingMachine.products[i].name,
                            stock = vendingMachine.products[i].stock,
                            price = vendingMachine.products[i].price;                            

                            if( (stock > 0) && (wallet >= price) ){
                                updateWallet = wallet - price;
                                wallet = updateWallet;
                                vendingMachine.users[i].wallet = wallet;
                                vendingMachine.products[i].stock = stock - 1;

                                console.log('Comprado '+productName+ '(Precio: '+price+' puntos)');
                                console.log('Puntos restantes tras la compra: '+wallet);
                                console.log('Stock de '+productName+ 'en máquina actualizado: '+vendingMachine.products[i].stock);
                            }
                            if(stock <= 0){
                                console.log('Producto '+productName+', agotado.');
                            }
                            if(wallet < price){
                                console.log('No dispones de puntos para realizar la compra.')
                            }
                            break;                   
                        }
                    }

                    break;
                }
                else{
                    console.log("Login incorrecto.");
                }
            }

            return msg;
        },
        deleteProduct: function (loginName, loginPass, idproduct){
            var checkProduct = vendingMachine.actions.checkProduct(idproduct),
                productName = checkProduct.name,
                msg = '';

            for (var i = 0; i < vendingMachine.users.length; i++){
                if( (loginName === vendingMachine.users[i].username) && (loginPass === vendingMachine.users[i].password) && (vendingMachine.users[i].role === 'admin') ){
                    console.log('Hola admin. Vamos a comprobar si existe ese producto antes de eliminarlo de la máquina.');
                    if ( checkProduct.match === true ){
                        vendingMachine.products.splice(checkProduct.position,1);
                        msg = 'Producto con el ID '+idproduct+' ('+productName+') eliminado';
                    }
                    else{
                        msg = 'El producto introducido no existe';
                    }                    
                    break;
                }
                else{
                    console.log("Login incorrecto.");
                }
            }

            return msg;
        },
        fetchStock: function(idproduct){
            var stock = '',
                productInfo = {};

            for (var i = 0; i < vendingMachine.products.length; i++){
                if ( vendingMachine.products[i].id === idproduct ){        
                    stock = vendingMachine.products[i].stock;                
                }
            }
            return stock;
        }                                          
    }

};

//shorteners 
var action = vendingMachine.actions,
    usersList = vendingMachine.users,
    userInfo = vendingMachine.actions.login,
    role =  userInfo.role,
    username = userInfo.username,
    wallet = userInfo.wallet,
    productsList = vendingMachine.products;


console.log( action.addUser('jgarcia','zzz','james','1234','client',100) );//Usuario james añadido ;)
console.log( action.addUser('jgarcia','zzz','neo','1234','client',100) );//¡Usuario neo no añadido! neo, ya existe!

action.login('jgarcia','1234');//login ko
action.login('neo','ccc');//login ok

action.userBalance('neo','ccc');//neo, tu saldo es de 100 puntos.

console.log( action.deleteUser('jgarcia','zzz','tyrell') );//El usuario introducido no existe
console.log( action.deleteUser('jgarcia','zzz','fSociety') );//Usuario fSociety eliminado

console.log(action.checkProduct(6));//skittles
console.log( action.addProduct('jgarcia','zzz','Oreo',20,30,10) );//{ id: 7, name: 'Oreo', stock: 20, max_units: 30, price: 10}

console.log(action.buyProduct('mrRobot','aaa',3));//{ id: 3, name: 'doritos', stock: 4, max_units: 10, price: 10}

console.log( action.deleteProduct('jgarcia','zzz', 2) );//producto con el ID 2(agua) eliminado
console.log( action.addPoints('jgarcia','zzz', 'mrRobot', 200) );//Añadidos 200 al usuario mrRobot. Total puntos en cartera: 300
console.log( action.subtractPoints('jgarcia','zzz', 'mrRobot', 50) );//Restados 50 al usuario mrRobot. Total puntos en cartera: 150
console.log( action.resetPoints('jgarcia','zzz', 'mrRobot') );//Cartera del usuario mrRobot vaciada.

console.log(action.buyProduct('mrRobot','aaa',4));//No dispones de puntos para realizar la compra.



```
