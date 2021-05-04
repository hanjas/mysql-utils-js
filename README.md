# MySQL Utility Functions

> An awesome MySQL utility function which will make your code easier to fetch and write data to database. This supports MySQL transaction in a clean way.

[![NPM](https://img.shields.io/npm/v/mysql-utils-js.svg)](https://www.npmjs.com/package/mysql-utils-js) [![JavaScript Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com)

## Install

```bash
npm i --save mysql-utils-js
```

## Usage

```jsx
const sqlutils = require('mysqlutils');

const config = {
    host: 'localhost',
    user: 'root',
    password: 'toor',
    database: 'db1',
    connectionLimit: 5
};

// A generic callback which contains three parameters [err, data, msg]
const functioncallback = (err, data, msg) {
    console.log({err, data, msg})
}
const query = "SELECT * FROM table1 WHERE id = ? and name = ?" // sample query
const params = [1, 'name_1']; // sample parameter for above query

sqlutils.createConnectionPool(config);
sqlutils.connectAndRun(functioncallback, 
    sqlutils.queryErrSucc(query, params, 
        (err) => {
            functioncallback(err, null, 'error happened'); 
        }, (data) => {
            functioncallback(null, data, 'success');
        }
    )
);
```

## License

ISC © [hanjas](https://github.com/hanjas)