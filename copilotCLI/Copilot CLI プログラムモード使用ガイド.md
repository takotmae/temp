# GitHub Copilot CLI �v���O�������[�h�i��C���^���N�e�B�u���[�h�j�g�p�K�C�h

GitHub Copilot CLI�ɂ́A�C���^���N�e�B�u��TUI���[�h�̑��ɁA�R�}���h���C������X�N���v�g�⎩�����Ŏg�p�ł���v���O�������[�h�i��C���^���N�e�B�u���[�h�j���񋟂���Ă��܂��B

## ? ��{�I�Ȏg�p���@

### �v�����v�g���[�h
```bash
copilot -p "�v�����v�g�̓��e"
```

**��:**
```bash
copilot -p "Create a Node.js express server with a health check endpoint"
copilot -p "Analyze this log file and find all error messages"
copilot -p "Write a Python script to fetch data from an API"
```

## ? �����̌o��

GitHub issue #96�uAdd non-interactive exec and input prompt support�v�ŁA���[�U�[��Codex CLI�̂悤�Ȕ�C���^���N�e�B�u�@�\��v�������ہAGitHub�̊J���`�[���i@lostintangent�j����ȉ��̉񓚂�����܂����F

> "If you run `copilot -p <prompt>`, that allows you to run prompts headlessly (vs. with the TUI)."

���̋@�\�͊��Ɏ�������Ă��܂������A�h�L�������g���s�\���Ŕ������Â炢��Ԃł����B

## ? �g�p��ƃ��[�X�P�[�X

### 1. ���O���
```bash
cat app.log | copilot -p "Find all lines with status 500 and count them"
```

### 2. �v���W�F�N�g�̑���쐬
```bash
copilot -p "Initialize a Node.js project, install express, and add a GET /health route"
```

### 3. jq�R�}���h�����x��
```bash
curl -s https://api.example.com/items | copilot -p "Write a jq command to extract id and name fields"
```

### 4. ���S�ȃ��|�W�g������
```bash
copilot -p "Create a grep command to find TODOs excluding node_modules directory"
```

### 5. Python���̃N�C�b�N�Z�b�g�A�b�v
```bash
copilot -p "Create a virtual environment, install requests library, and fetch data from https://example.com"
```

## ? �p�C�v���C������

### �W�����͂���̓ǂݍ���
�v�����v�g�� `-` ���w�肷�邱�ƂŁA�W�����͂���v�����v�g��ǂݍ��ނ��Ƃ��ł��܂��F

```bash
echo "Explain this error message" | copilot -p -
```

### �t�@�C������̃v�����v�g�ǂݍ���
```bash
copilot -p - < prompt.txt
```

### ���G�ȃp�C�v���C����
```bash
# ���O�t�@�C������͂��ăG���[�𒊏o
tail -100 /var/log/app.log | copilot -p "Summarize the errors and suggest fixes"

# Git���O�𕪐�
git log --oneline -10 | copilot -p "Analyze these commits and suggest a release note"

# �R�[�h���r���[�x��
git diff HEAD~1 | copilot -p "Review this code change and suggest improvements"
```

## ? �������ƃX�N���v�g����

### Bash�X�N���v�g�ł̎g�p
```bash
#!/bin/bash
# deploy-helper.sh

LOG_FILE="/var/log/deploy.log"
ANALYSIS=$(tail -50 "$LOG_FILE" | copilot -p "Check if deployment was successful and summarize any issues")

echo "Deployment Analysis:"
echo "$ANALYSIS"

# ���ʂɊ�Â��ď�������
if echo "$ANALYSIS" | grep -qi "error\|failed"; then
    echo "Deployment issues detected. Please review."
    exit 1
fi
```

### CI/CD�ł̎g�p��
```yaml
# .github/workflows/code-review.yml
name: AI Code Review
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  ai-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Install Copilot CLI
        run: npm install -g @github/copilot
        
      - name: AI Code Review
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          git diff origin/main...HEAD | copilot -p "Review this code change for potential issues and improvements" > review.md
          gh pr comment --body-file review.md
```

### Makefile�ł̎g�p
```makefile
# Makefile
.PHONY: analyze-logs
analyze-logs:
	@tail -100 app.log | copilot -p "Analyze these logs and identify any concerning patterns"

.PHONY: generate-docs
generate-docs:
	@find src -name "*.js" -exec cat {} \; | copilot -p "Generate API documentation for these JavaScript files"
```

## ?? ���p�\�ȃI�v�V����

���݊m�F����Ă���I�v�V�����F
- **`-p, --prompt`**: �v�����v�g���w��i�K�{�j
- �v�����v�g�� `-` ���w�肷��ƕW�����͂���ǂݍ���

## ? ���H�I�Ȋ��p�V�i���I

### �J�����[�N�t���[����
```bash
# 1. �R�~�b�g�O�̎����`�F�b�N
git diff --cached | copilot -p "Review staged changes for potential issues"

# 2. �e�X�g���ʂ̕���
npm test 2>&1 | copilot -p "Analyze test results and suggest fixes for failures"

# 3. �p�t�H�[�}���X����
curl -w "@curl-format.txt" -s https://api.example.com/health | copilot -p "Analyze API performance metrics"
```

### �V�X�e���Ǘ�
```bash
# 1. �V�X�e���w���X �`�F�b�N
df -h && free -h && uptime | copilot -p "Analyze system health and recommend actions"

# 2. �Z�L�����e�B���O����
journalctl -u ssh.service --since "1 hour ago" | copilot -p "Check for suspicious SSH activity"

# 3. �v���Z�X����
ps aux --sort=-%cpu | head -20 | copilot -p "Identify resource-intensive processes and optimization opportunities"
```

## ?? ���ӎ����Ɛ���

### �F�ؗv��
- GitHub Copilot �T�u�X�N���v�V�������K�v
- ���O�� `copilot` �Ń��O�C�����Ă����K�v������
- ���ϐ� `GH_TOKEN` �܂��� `GITHUB_TOKEN` �ł̔F�؂��\

### �Z�L�����e�B�l������
- �@�������܂ރf�[�^���v�����v�g�Ɋ܂߂Ȃ��悤����
- �{�Ԋ��ł̃X�N���v�g���s�O�ɏ\���ȃe�X�g���{�𐄏�
- �������ꂽ�R�}���h�͎��s�O�ɕK���m�F����

### �p�t�H�[�}���X
- �e�v�����v�g�Ńv���~�A�����N�G�X�g�N�H�[�^��1����
- ��ʂ̃f�[�^����x�ɑ��M����Ɖ������Ԃ������Ȃ�\��
- �o�b�`�����ł͓K�؂ȊԊu��݂��邱�Ƃ𐄏�

## ? ����̓W�]

GitHub issue #96�̃f�B�X�J�b�V�����ɂ��ƁA�����I�ɂ͈ȉ��̋@�\����������Ă��܂��F

### �\�z����鏫���̋@�\
- **`copilot exec`** �T�u�R�}���h
- **`--model`** �I�v�V�����i���f���I���j
- **`--json`** �I�v�V�����iJSON�o�́j
- **`--full-auto`** �I�v�V�����i�������s�j
- **`--sandbox`** �I�v�V�����i�T���h�{�b�N�X���s�j

### ���݂̏�
- ��{�I�� `-p` �I�v�V������ v0.0.329 �ȑO���痘�p�\
- �G���[���b�Z�[�W�� v0.0.329 �ŉ��P�ς�
- ���ڍׂȃI�v�V�����͏����̃����[�X�Œǉ��\��

## ? �܂Ƃ�

GitHub Copilot CLI�̃v���O�������[�h���g�p���邱�ƂŁF

1. **�X�N���v�g������**: Bash�APython�ACI/CD�p�C�v���C���ł̓���
2. **���O����**: ���A���^�C���܂��̓o�b�`�ł̃��O�t�@�C�����
3. **�R�[�h����**: �J��Ԃ���Ƃ̎�����
4. **�V�X�e���Ď�**: �w���X�`�F�b�N�� �p�t�H�[�}���X����

���̋@�\�ɂ��AGitHub Copilot CLI���C���^���N�e�B�u�ȊJ���x���c�[���Ƃ��Ă����łȂ��ADevOps��V�X�e���Ǘ��̎������c�[���Ƃ��Ă����p�ł��܂��B

---

**����**: ���̋@�\�̓p�u���b�N�v���r���[�i�K�̂��߁A�����I�ɕύX�����\��������܂��B�ŐV�̏��ɂ��Ă͌����h�L�������g���m�F���Ă��������B