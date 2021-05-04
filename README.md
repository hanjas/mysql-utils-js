# mysql-utils-js

> MySQL utility functions which will make your code easier to fetch and write data to database. This supports MySQL transaction in a cleaner way.

[![NPM](https://img.shields.io/npm/v/mysql-utils-js.svg)](https://www.npmjs.com/package/mysql-utils-js) [![JavaScript Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com)

## Install

```bash
npm i --save mysql-utils-js
```

## Usage

Sample code for performing single query

```jsx
const sqlutils = require('mysql-utils-js');

const config = {
    host: 'localhost',
    user: 'username',
    password: 'pwd',
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

Sample code for performing query transaction.

```jsx
const sqlutils = require('mysql-utils-js');

const config = {
    host: 'localhost',
    user: 'username',
    password: 'pwd',
    database: 'db1',
    connectionLimit: 5
};

sqlutils.createConnectionPool(config);

let queryList = [
    ['queryid1',`SELECT id FROM users WHERE name = ?`,
    _ => ['name1'],                                         // passing param value for query number 1
        err => [true, err, 'Some error message'],           // this will be called if any error happened while performing query
        (_resAll, res) => (res && res.affectedRows > 0) ?   // _ressAll contains result of all query and res contains current query result
            [false, res, 'Some success message']:           // returning success
            [true, res, 'Some failure message']             // returning failure
    ],
    ['updateinfoquery',`DELETE FROM users WHERE id = ?`,
        (results) => {
            let useid = results['queryid1'][1].insertId;    // taking userid from previous query result (from queryid1 result)
            return [userid]                                 // passing param value for query number 2
        },
        err => [true, err, 'Some error message'],           // this will be called if any error happened while performing query
        (_resAll, res) => (res && res.affectedRows > 0) ?   // _ressAll contains result of all query and res contains current query result
            [false, res, 'Some success message']:           // returning sucess
            [true, res, 'Some error message']               // returning failure
    ]
];

// This is a function which will run the list of queries in TRANSACTION
// THIS IS A TRANSACTION, Queries can fail independently, so even if one query fails the whole sequence of queries will be rolled back
// Input: callbackinp, listofqueries
// This function calls connection.query for all the queries in the listofqueries in sequence and returns the results through the callback
// list of queries format => [[queryid, querysql, queryarg, failfunc, succfunc],...] queryid => the results of this particular query will be under queryid key
// failfunc => will be called to check if we get error object in the query callback, and then we check if it actually an error
// succfunc => will be called when mysql error obj is NULL, this can also fail the transaction if it returns [true,. ,.]
// The above function should return [shouldifailtransaction, result if succ else errobj, succmsg if succ else errmsg]
// If ALL SUCCESS, callback will be called with (null, resultobj, succmsg); resultobj => {'queryid1':[resultforthisquery', 'queryid2':[]}
// Even if one FAILS, callback will be called with ({'errs':errobj}, null, failmsg) and the transaction would have been rolled back

sqlutils.getMysqlConnAndRunTran((err, data, msg) => {
    // handle all query result here
}, queryList);
```

## License

ISC © [hanjas](https://github.com/hanjas)