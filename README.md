# token-vend
Vue.js application for accepting SLP Token payments using the Bitsocket & SLPDB API then notifies the iozeta CryptoVend of the specified token payments for use in vending machines.

### Running and Installation
This project is a Vue.js application, running on top of [Node JS](https://nodejs.org) and [NPM](https://www.npmjs.com/). After installing Node and NPM locally, the following steps will setup and configure the application.

```git clone https://github.com/SLPVH/token-vend.git```

```cd token-vend```

```npm install```

Edit the .env.local file. This file contains your SLP token settings and iozeta CryptoVend settings.

```vim .env.local``` 

Add the following lines, setting the config values to your own.

```
VUE_APP_IOZETA_VEND_IP=[CRYPTOVEND LOCAL IP]
VUE_APP_IOZETA_VEND_USER=[CRYPTOVEND USER]
VUE_APP_IOZETA_VEND_PASS=[CRYPTOVEND PASS]
VUE_APP_SLP_SYMBOL=[SLP TOKEN SYMBOL]
VUE_APP_BCH_ADDRESS=[BITCOIN CASH PAYMENT ADDRESS]
VUE_APP_SLP_ADDRESS=[SLP PAYMENT ADDRESS]
```

The following command can be used to build the project into a web application. See https://vuejs.org/ for detailed documentation on running Vue JS applications.

``vue-cli-service build``

### Demonstration

[![SPICE Token Demo](https://img.youtube.com/vi/3qpG0NClXXc/0.jpg)](https://www.youtube.com/watch?v=3qpG0NClXXc)
