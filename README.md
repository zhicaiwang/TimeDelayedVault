# TimeDelayedVault
# 合约地址
0x997f5d2A4865D48873E4D4130A791C801D209E12

# 攻击方案
## re-entry
    function withdrawFund() onlyAuthorized external returns (bool) {
        require(now > nextWithdrawTime);
        assert(withdrawObserver.call(bytes4(sha3("observe()"))));
        walletLibrary.delegatecall(bytes4(sha3("basicWithdraw()")));
        nextWithdrawTime = nextWithdrawTime + withdrawCoolDownTime;
        return true;
    }
因为这里的金额转账发生在时间修改之前。
根据第七课特别提到的re-entry攻击。通过智能合约去调用该方法，然后获得所有权之后，重复调用。在block gas limit限制下，尽量多的获取ETH。

先自行在测试网络上部署，在自己的测试网络上尝试
1. 先建立投票选出取钱的地址，每个小组成员都调用该函数
addAuthorizedAccount
2. 调用setObserver函数，随便一个地址
3. re-entry攻击

尚未成功。

## 下一个方案
