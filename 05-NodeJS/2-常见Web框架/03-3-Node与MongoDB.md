## 一 Node 连接 mongodb

```JavaScript
const MongoClient = require('mongodb').MongoClient;
const MongoURL = 'mongodb://localhost:27017/';
function _connectDB(callback) {
    MongoClient.connect(MongoURL,function (err,db) {
        if(err){
            console.log('Mongo未连接');
            callback(err,'Mongo未连接');
            return;
        }
        console.log('Mongo已连接');
        callback(null, db);
    });
}

//插入一个数据
exports.insertOne = function (dbname,collectionName,json,callback) {
    _connectDB(function (err,db) {
        if(err){
            throw err;
            return;
        }
        let dbObj = db.db(dbname);
        dbObj.collection(collectionName).insertOne(json, function (err, result) {
            if (err) {
                throw err;
                db.close();
                return;
            }
            callback(null, result);
            db.close();
        });
    });
};

```

//使用：

```JavaScript
const db = require('./db');
db.insertOne('testdb','teacher',{'name':'lisi'},function (err,result) {
    if(err){
        console.log(err);
        return;
    }
    console.log('sucess');
});

```