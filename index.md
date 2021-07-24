### Blockchain

This is a simple project.
Objectives :
1. Understand the workings of a Blockchain
2. Blockchain's Uses and Benefits
3. Create a simple Blockchain

```markdown

'''This Simple Blockchain can be run through a Postman Agent'''
# Import the necessary frameworks and library
import datetime
import hashlib
import json
from flask import Flask, jsonify

```

```markdown
# Create a blockchain class
## Building a blockchain
class Blockchain:
    def __init__(self):
        self.chain = []
        self.create_block(proof=1, previous_hash='0')

    def create_block(self,proof,previous_hash):
        block = {'index':len(self.chain)+1,
                 'timestamp': str(datetime.datetime.now()),
                 'proof':proof,
                 'previous_hash':previous_hash}
        self.chain.append(block)
        return block

    def get_previous_block(self):
        return self.chain[-1]

    def proof_of_work(self, previous_proof):
        new_proof = 1
        check_proof = False
        while check_proof is False:
            hash_operation = hashlib.sha256(str(new_proof**2 - previous_proof**2).encode()).hexdigest()
            if hash_operation[:4] == "0000":
                check_proof = True
            else:
                new_proof += 1
        return new_proof

    def hash(self, block):
        encoded_block = json.dumps(block, sort_keys=True).encode()
        return hashlib.sha256(encoded_block).hexdigest()

    def is_chain_valid(self, chain):
        previous_block = chain[0]
        block_index = 1
        while block_index < len(chain):
            block = chain[block_index]
            if block['previous_hash'] != self.hash(previous_block):
                return False
            previous_proof = previous_block['proof']
            proof = block['proof']
            hash_operation = hashlib.sha256(str(proof ** 2 - previous_proof ** 2).encode()).hexdigest()
            if hash_operation[:4] != '0000':
                return False
            previous_block = block
            block_index += 1
        return True
```

```markdown
#Mining a blockchain
##creating web app
app = Flask(__name__)
```

```markdown
##initiating a blockchain
blockchain = Blockchain()
```
```markdown
# Functions to manipulate the Blockchain
## mine a block
@app.route('/mine_block', methods=['GET'])
def mine_block():
    previous_block = blockchain.get_previous_block()
    previous_proof = previous_block['proof']

    proof = blockchain.proof_of_work(previous_proof)
    previous_hash = blockchain.hash(previous_block)
    block = blockchain.create_block(proof,previous_hash)
    response = {'message':'Congratulations, you just mined a block!',
                'index':block['index'],
                'timestamp':block['timestamp'],
                'proof':block['proof'],
                'previous_hash':block['previous_hash']}
    return jsonify(response), 200
```

```markdown
## Display the full Blockchain
@app.route('/get_chain', methods=['GET'])
def get_chain():
    response = {'chain_key':blockchain.chain,
                'length':len(blockchain.chain)}
    return jsonify(response), 200
```

```markdown
## check if Blockchain is valid
@app.route('/is_valid', methods=['GET'])
def is_valid():
    validity = blockchain.is_chain_valid(blockchain.chain)
    if validity:
        response = {'message':'All good, the Blockchain is valid.'}
    else:
        response = {'message':'Houston we have a problem, the Blockchain is corrupted.'}
    return jsonify(response),200
```

```markdown
# Running The App
app.run(port=5000,host='0.0.0.0')

'''To Run The App.
1. Run all the code blocks above
2. Open postman agent
3. Create a workspace
4. In the Requests box, connect to the flask app by entering 'http://127.0.0.1:5000/'
5. To start, you might want to see the whole chain first by entering 'http://127.0.0.1:5000/get_chain'
6. After that you can also mine a block 'http://127.0.0.1:5000/mine_block'
7. and also check if the blockchain is valid 'http://127.0.0.1:5000/is_valid'
8. I encourage messing around with it!
'''
```


