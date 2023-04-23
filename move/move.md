# move 智能合约语言

# sui move使用
## 1、基础
### 1.1、安装
```shell
cargo install --locked --git https://github.com/MystenLabs/sui.git --branch devnet sui
```

插件安装
```shell
cargo install --git https://github.com/move-language/move move-analyzer --branch sui-move --features "address32"
```
vscode中选择move-analyzer插件


### 1.2、创建项目
```shell
sui move new <PACKAGE NAME>
```


### 1.3、发布合约
```shell
sui client publish --gas-budget 30000000
```

发布时会执行编译
```shell
sui move build
```

错误：
```text
Failed to build Move modules: Failed to resolve dependencies for package 'hello_word'

Caused by:
    0: Parsing manifest for 'Sui'
    1: Unable to find package manifest at "./../sui-testnet/crates/sui-framework"
    2: No such file or directory (os error 2).
``` 
原因：依赖不正确,可以配置成本地依赖，注意路径
```text
Sui = { local = "../sui-testnet/crates/sui-framework/packages/sui-framework" }
```

### 1.4、调用合约
```shell
sui client call --function mint --module hello_word --package <PACKAGE_ID> --gas-budget 30000000
```


## 2、语法
### 2.1 Objects所有权
+ 被拥有
    - 被一个地址拥有  
      transfer::transfer(transcriptObject, tx_context::sender(ctx)) // 被交易发起者拥有

    - 被另一个 object 拥有  
      dynamic_object_field


+ 共享
    - 不可变的共享    
      transfer::freeze_object(obj); // packages 和 modules 都属于不可变的 objects

    - 可变的共享  
      transfer::share_object(obj); // 被任何人读和改

### 2.2 删除Object
```shell
use sui::object::{Self};
 
public entry fun delete_transcript(transcriptObject: TranscriptObject){
    let TranscriptObject {id, history: _, math: _} = transcriptObject;
    object::delete(id);
}
```

### 2.3 Object Wrapping嵌套使用

### 2.4 错误码
const ENotIntendedAddress: u64 = 1;

### 2.5 权限
```shell
struct TeacherCap has key {
    id: UID
}

public entry fun create_wrappable_transcript_object(_: &TeacherCap, history: u8, math: u8, literature: u8, ctx: &mut TxContext) {
    let wrappableTranscript = WrappableTranscript {
        id: object::new(ctx),
        history,
        math,
        literature,
    };
    transfer::transfer(wrappableTranscript, tx_context::sender(ctx))
}

fun init(ctx: &mut TxContext) {
    transfer::transfer(TeacherCap {
        id: object::new(ctx)
    }, tx_context::sender(ctx))
}

public entry fun add_additional_teacher(_: &TeacherCap, new_teacher_address: address, ctx: &mut TxContext){
    transfer::transfer(
        TeacherCap {
            id: object::new(ctx)
        },
    new_teacher_address
    )
}
```

### 2.6 事件
```shell
// Event marking when a transcript has been requested
struct TranscriptRequestEvent has copy, drop {
    // The Object ID of the transcript wrapper
    wrapper_id: ID,
    // The requester of the transcript
    requester: address,
    // The intended address of the transcript
    intended_address: address,
}

public entry fun request_transcript(transcript: WrappableTranscript, intended_address: address, ctx: &mut TxContext){
    let folderObject = Folder {
        id: object::new(ctx),
        transcript,
        intended_address
    };
    event::emit(TranscriptRequestEvent {
        wrapper_id: object::uid_to_inner(&folderObject.id),
        requester: tx_context::sender(ctx),
        intended_address,
    });
    //We transfer the wrapped transcript object directly to the intended address
    transfer::transfer(folderObject, intended_address);
}
```

### 2.7 泛型
```shell
public fun create_box<T>(value: T): Box<T> {
        Box<T> { value }
    }
    
public fun create_box(value: u64): Box<u64> {
        Box<u64>{ value }
    }
```

### 2.8 设计模式
#### 2.8.1 witness
只使用一次
```text
struct PEACE has drop {}
```

#### 2.8.2 phantom

