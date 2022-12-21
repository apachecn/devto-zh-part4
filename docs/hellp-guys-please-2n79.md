# 你们好，伙计们

> 原文：<https://dev.to/castromatavele/hellp-guys-please-2n79>

Hellp，我在 cpanal 上托管我的应用程序，现在我有这个错误，我过去发送电子邮件，但现在给出这个错误

"出于安全原因，proc_open()已被禁用"

```
private function establishProcessConnection()
{
    $command = $this->params['command'];
    $descriptorSpec = [
        0 => ['pipe', 'r'],
        1 => ['pipe', 'w'],
        2 => ['pipe', 'w'],
        ];
    $pipes = [];
    $this->stream = proc_open($command, $descriptorSpec, $pipes);
    stream_set_blocking($pipes[2], 0);
    if ($err = stream_get_contents($pipes[2])) {
        throw new Swift_TransportException(
            'Process could not be started ['.$err.']'
            );
    }
    $this->in = &$pipes[0];
    $this->out = &$pipes[1];
}

private function getReadConnectionDescription()
{
    switch ($this->params['type']) {
        case self::TYPE_PROCESS:
            return 'Process '.$this->params['command']; 
```