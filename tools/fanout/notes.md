python -c "
import json, pathlib, subprocess
for n in ['alpha', 'beta']:
    p = pathlib.Path('/tmp/fleet') / n; p.mkdir(parents=True, exist_ok=True)
    subprocess.run(['git', 'init', '-q', '-b', 'main'], cwd=p)
    (p / 'hello.txt').write_text('hi\n')
    subprocess.run(['git', 'add', '-A'], cwd=p)
    subprocess.run(['git', '-c', 'user.name=d', '-c', 'user.email=d@d', 'commit', '-qm', 'init'], cwd=p)
open('/tmp/targets.jsonl', 'w').write('\n'.join(json.dumps({'id': n, 'path': f'/tmp/fleet/{n}'}) for n in ['alpha', 'beta']) + '\n')
"