# uuid-mongodb

![](https://img.shields.io/badge/build-passing-brightgreen)
[![Codacy Badge](https://app.codacy.com/project/badge/Grade/a8c86402f1504155b9d16dc3a9a88b0e)](https://www.codacy.com/gh/apigames-core/uuid-mongodb/dashboard?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=apigames-core/uuid-mongodb&amp;utm_campaign=Badge_Grade)
![](https://img.shields.io/badge/license-MIT-blue)

This is package is a fork of the [uuid-mongodb](https://github.com/cdimascio/uuid-mongodb) package created by [Carmine DiMascio](https://github.com/cdimascio), [Benjamin Dobell](https://glassechidna.com.au/) & [David Pfeffer](https://github.com/bytenik) with some minor contributions from [Erich Kuba](https://github.com/erichkuba) of [Cloudize Limited](https://cloudize.net).  The purpose of the fork is to provide a package that aligns with the release cadence and dependencies of the API Games Framework.

## Install

```shell 
$ npm i @cloudize/uuid-mongodb 
```

## Usage

Please see https://github.com/cdimascio/uuid-mongodb for usage notes.

## Authors or Acknowledgments

*   [Carmine DiMascio](https://github.com/cdimascio)
*   [Benjamin Dobell](https://glassechidna.com.au/)
*   [David Pfeffer](https://github.com/bytenik)
*   [Erich Kuba](https://github.com/erichkuba)

// Create a v1 binary UUID  
```const mUUID1 = MUUID.v1();```

// Create a v4 binary UUID  
```const mUUID4 = MUUID.v4();```

// Print a string representation of a binary UUID  
```mUUID1.toString()```

// Create a binary UUID from a valid uuid string  
```const mUUID2 = MUUID.from('393967e0-8de1-11e8-9eb6-529269fb1459')```

// Create a binary UUID from a MongoDb Binary  
// This is useful to get MUUIDs helpful toString() method  
```const mUUID3 = MUUID.from(/** MongoDb Binary of SUBTYPE_UUID */)```

## Formatting

UUIDs may be formatted using the following options:

Format | Description | Example
-- | -- | --
N | 32 digits | `00000000000000000000000000000000`
D | 32 digits separated by hyphens | `00000000-0000-0000-0000-000000000000`
B | 32 digits separated by hyphens, enclosed in braces | `{00000000-0000-0000-0000-000000000000}`
P | 32 digits separated by hyphens, enclosed in parentheses | `(00000000-0000-0000-0000-000000000000)`

**example:**
```javascript
const mUUID4 = MUUID.v4();
mUUID1.toString(); // equivalent to `D` separated by hyphens
mUUID1.toString('P'); // enclosed in parens, separated by hypens
mUUID1.toString('B'); // enclosed in braces, separated by hyphens
mUUID1.toString('N'); // 32 digits
```

## Modes

uuid-mongodb offers two modes:

- **canonical** (default) - A string format that emphasizes type preservation at the expense of readability and interoperability.
- **relaxed** - A string format that emphasizes readability and interoperability at the expense of type preservation.

The mode is set **globally** as such:

```javascript
const mUUID = MUUID.mode('relaxed'); // use relaxed mode
```

Mode _**only**_ impacts how `JSON.stringify(...)` represents a UUID:

e.g. `JSON.stringify(mUUID.v1())` outputs the following:

```javascript
"DEol4JenEeqVKusA+dzMMA==" // when in 'canonical' mode
"1ac34980-97a7-11ea-8bab-b5327b548666" // when in 'relaxed' mode
```

## Examples

**Query using binary UUIDs**

```javascript
const uuid = MUUID.from('393967e0-8de1-11e8-9eb6-529269fb1459');
return collection.then(c =>
  c.findOne({
    _id: uuid,
  })
);
```

**Work with binary UUIDs returned in query results**

```javascript
return collection
  .then(c => c.findOne({ _id: uuid }))
  .then(doc => {
    const uuid = MUUID.from(doc._id).toString();
    // do stuff
  });
```

## Examples (with source code)

#### Native Node MongoDB Driver example

- [examples/ex1-mongodb.js](examples/ex1-mongodb.js)

	**snippet:**
	
	```javascript
	const insertResult = await collection.insertOne({
	  _id: MUUID.v1(),
	  name: 'carmine',
	});
	```

#### Mongoose example

- [examples/ex2-mongoose.js](examples/ex2-mongoose.js)

	**snippet:**
	
	```javascript
	const kittySchema = new mongoose.Schema({
	  _id: {
	    type: 'object',
	    value: { type: 'Buffer' },
	    default: () => MUUID.v1(),
	  },
	  title: String,
	});
	```

- [examples/ex3-mongoose.js](examples/ex3-mongoose.js)

	**snippet:**
	
	```javascript
	// Define a simple schema
	const kittySchema = new mongoose.Schema({
	  _id: {
	    type: 'object',
	    value: { type: 'Buffer' },
	    default: () => MUUID.v1(),
	  },
	  title: String,
	});
	
	// no need for auto getter for _id will add a virtual later
	kittySchema.set('id', false);
	
	// virtual getter for custom _id
	kittySchema
	  .virtual('id')
	  .get(function() {
	    return MUUID.from(this._id).toString();
	  })
	  .set(function(val) {
	    this._id = MUUID.from(val);
	  });
	```

- [examples/ex4-mongoose.js](examples/ex4-mongoose.js)

```javascript
    const uuid = MUUID.v4();

    // save record and wait for it to commit
    await new Data({ uuid }).save();

    // retrieve the record
    const result = await Data.findOne({ uuid });
```

## Notes

Currently supports [UUID v1 and v4](https://www.ietf.org/rfc/rfc4122.txt)


## License

This project is licensed under the MIT License
