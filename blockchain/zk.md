## 零知识
### 知识证明的“家族树”
类型	特点	代表算法
zk-SNARKs	简短证明、快速验证，但需要「可信设置」	Groth16, PLONK, Halo2
zk-STARKs	无需可信设置、抗量子计算，证明较长	STARK


### 主流零知识证明算法对比
算法	全称	是否需要可信设置	证明大小	验证速度	抗量子	典型应用
Groth16	第一代高效 SNARK	✅ 是	很小 (~192字节)	极快	❌ 否	Zcash,早期项目
PLONK	普适性电路 SNARK	✅ 是（一次）	小 (~400 字节)	快	❌ 否	zkSync, Mina
Halo / Halo2	递归 SNARK，无须可信设置	❌ 否	中等	快	✅ 是（部分）	Polygon zkEVM, Mina
STARK	可扩展透明 ARgument	❌ 否	较大 (~几十KB)	较快	✅ 是	StarkNet, StarkEx
Bulletproofs	不需可信设置，但证明长	❌ 否	长	慢	✅ 是	Monero（隐私金额）




项目	使用的 ZKP 算法	是否需要可信设置	优势	劣势
zkSync	PLONK	✅（一次性）	快速、生态成熟	依赖可信设置
StarkNet	STARK	❌	无需信任、抗量子	证明大、开发复杂
Polygon zkEVM	Halo2	❌	无需可信设置、安全	验证成本略高
Scroll	PLONK-like SNARK	✅	高度兼容 EVM	仍需可信设置
Linea	PLONK/UltraPLONK	✅	开发友好	同上
Filecoin	
Groth16 / SnarkyJS	✅	高效验证存储	旧协议，升级中
Mina	Pickles (递归 SNARK)	✅ → 过渡到无	超轻量链	复杂度高



需求	推荐方案
✅ 快速验证 + 小证明	zk-SNARKs（如 PLONK）
✅ 去中心化 + 安全性优先	Halo2 或 STARK
✅ 兼容以太坊 EVM	zkSync、Polygon zkEVM、Scroll
✅ 抗量子未来	STARK 或 Halo2
✅ 存储证明类应用	Groth16 或自定义 SNARK



技术路线	代表项目	适合谁？
SNARK（PLONK）	zkSync, Scroll	想快速上线、兼容 EVM 的团队
SNARK（Halo2）	Polygon zkEVM, Mina	注重长期安全、无需可信设置
STARK	StarkNet	追求极致去中心化和抗量子
我总结了一些零知识证明的知识，帮我汇总总结和补充一些，我好学习相关的技术