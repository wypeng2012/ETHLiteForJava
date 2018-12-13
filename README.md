# ETHLiteForJava
eth library for java


中文文档：[https://blog.csdn.net/wypeng2010/article/details/84985009](https://blog.csdn.net/wypeng2010/article/details/84985009)

**- Use it**

1. first add Bip44ForJava dependencies

> implementation 'party.52it:Bip44ForJava:1.0.0'

2. generate ETH

## code: ##
```java
            System.out.println("************ 获取12个助记词中 ************");
            List<String> words = Bip44Utils.generateMnemonicWords();
            System.out.println("12个助记词: " + words.toString() + "\n");

            System.out.println("************ 种子生成中 ************");
            byte[] seed = Bip44Utils.getSeed(words);
            System.out.println("种子: " + new BigInteger(1,seed).toString(16) + "\n");

            System.out.println("************ 根据路径获取节点私钥中 ************");
            BigInteger pri1 = Bip44Utils.getPathPrivateKey(words,"m/44'/60'/0'/0/0");
            System.out.println("路径私钥: " + pri1.toString(16) + "\n");

            ECKeyPair ecKeyPair = ECKeyPair.create(pri1);

            String publicKey = ecKeyPair.getPublicKeyToString();
            System.out.println("publicKey: " + publicKey);

            String privateKey = ecKeyPair.getPrivateKeyToString();
            System.out.println("privateKey: " + privateKey);

            String address = "0x" + Keys.getAddress(ecKeyPair);
            System.out.println("address: " + address);
```
## result: ##
```base
************ 获取12个助记词中 ************
12个助记词: [bottom, material, render, live, alcohol, fan, lab, monkey, laptop, leader, stay, light]

************ 种子生成中 ************
PBKDF2 took {}678.9 ms
种子: 625becd48df47b1304a13daf6d6b1f5df735a1a3e0ecb1307afdbcee5c71d342296f4982528c91728c5eb01ad72ccd7beebac5b4b7c594095d39408dbb0d6ec8

************ 根据路径获取节点私钥中 ************
PBKDF2 took {}29.31 ms
路径私钥: a54faf1496634d32743ba74755b82f673c1309335e49e19cf527d17f3304899d

publicKey: 0x7dcb498a35f9beb9e5b567055fbefc50380c5e7bf23cac9ef4ced3cce3acb52acf0e77b6b594a5f68838f6cd211ce824e301f6e2fdd458bf64ff43d4ae9b60ed
privateKey: 0xa54faf1496634d32743ba74755b82f673c1309335e49e19cf527d17f3304899d
address: 0x71e6ed40bdb631e1cd15046b6b053f80333ceece
```
3. sign eth transaction

```java
public static String signedEthTransactionData(String privateKey, String to, BigInteger nonce, BigInteger gasPrice, BigInteger gasLimit, String value) throws Exception {
        
        //1ETH = 10^18 Wei
        BigDecimal realValue = Convert.toWei(value, Convert.Unit.ETHER);
        RawTransaction rawTransaction = RawTransaction.createEtherTransaction(nonce, gasPrice, gasLimit, to, realValue.toBigIntegerExact());
        //fee= (gasPrice * gasLimit ) / 10^18 ether

        Credentials credentials = Credentials.create(privateKey);
        byte[] signedMessage = TransactionEncoder.signMessage(rawTransaction, credentials);
        
        return Numeric.toHexString(signedMessage);
    }
```
4. sign eth contract transaction

```java
public static String signedEthContractTransactionData(String privateKey, String contractAddress, String to, BigInteger nonce, BigInteger gasPrice, BigInteger gasLimit, Double value, Double decimal) throws Exception {
        if (words == null || words.size() != 12) {
            throw new RuntimeException("please generateMnemonic first");
        }
        
        BigDecimal realValue = BigDecimal.valueOf(value * Math.pow(10.0, decimal));

        
        String data = "0xa9059cbb" + Numeric.toHexStringNoPrefixZeroPadded(Numeric.toBigInt(to), 64) + Numeric.toHexStringNoPrefixZeroPadded(realValue.toBigInteger(), 64);
        RawTransaction rawTransaction = RawTransaction.createTransaction(nonce, gasPrice, gasLimit, contractAddress, data);
        //fee= (gasPrice * gasLimit ) / 10^18 ether

        Credentials credentials = Credentials.create(privateKey);

        byte[] signedMessage = TransactionEncoder.signMessage(rawTransaction, credentials);
       
        return Numeric.toHexString(signedMessage);
    }
```

 **- How to dependencies**
1. Maven

```base
<dependency>
  <groupId>party.52it</groupId>
  <artifactId>ETHLiteForJava</artifactId>
  <version>1.0.0</version>
</dependency>
```
2. Gradle

```base
compile 'party.52it:ETHLiteForJava:1.0.0'

or

implementation 'party.52it:ETHLiteForJava:1.0.0'

```
3. Ivy

```base
<dependency org='party.52it' name='ETHLiteForJava' rev='1.0.0'/>
```




 **- License**

     Copyright 2018 52it.party
    
    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at
    
       http://www.apache.org/licenses/LICENSE-2.0
    
    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitatio
