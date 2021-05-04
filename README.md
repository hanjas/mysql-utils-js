# MySQL Utility Functions

> An awesome MySQL utility function which will make your code easier to fetch and write data to database. This supports MySQL transaction in a clean way.

[![NPM](https://https://img.shields.io/npm/v/mysql-utils-js.svg)](https://www.npmjs.com/package/mysql-utils-js) [![JavaScript Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com)

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

# Props

### date

The default date, should be passed in this format - 'yyyy-mm-dd'. example - '2019-12-23'

### config

The Configuration for the date picker

| Prop         | Defenition                                                            | Data type |
|--------------|-----------------------------------------------------------------------|-----------|
| alwaysShow   | If passed as true, The DatePicker will be always on the visible state | boolean   |
| showCalendar | If passed as true, The DatePicker will be on visible state initially  | boolean   |
| themePreset  | To set the default theme, 'dark' and 'default' is available           | string    |
| reminders    | Array of reminder objects                                             | array     |
| theme        | Theme config object                                                   | object    |

### config.reminders

The Reminders / Notes that will show in the given date

| Prop      | Defenition                                       | Data Type |
|-----------|--------------------------------------------------|-----------|
| note      | The text which will be displayed as the reminder | string    |
| color     | The color of the tag of reminder                 | string    |
| timestamp | The timestamp of the day                         | int       |

## License

ISC © [hanjas](https://github.com/hanjas)