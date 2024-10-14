# シェルスクリプトのコーディングルール

## 包括的なエラー処理と入力検証

例

```
if [ -z "$1" ]
  then
    echo "Usage: evaluate.sh <fork name> (<fork name 2> ...)"
    echo " for each fork, there must be a 'calculate_average_<fork name>.sh' script and an optional 'prepare_<fork name>.sh'."
    exit 1
fi
```

## 明確でカラフルな出力

```
BOLD_WHITE='\033[1;37m'
CYAN='\033[0;36m'
GREEN='\033[0;32m'
PURPLE='\033[0;35m'
BOLD_RED='\033[1;31m'
RED='\033[0;31m'
BOLD_YELLOW='\033[1;33m'
RESET='\033[0m' # No Color

echo -e "${BOLD_RED}ERROR${RESET}: ./calculate_average_$fork.sh does not exist." >&2
```

## 詳細な進捗報告

```
function print_and_execute() {
  echo "+ $@" >&2
  "$@"
}
```

## 「set -e」と「set +e」による戦略的エラー処理

```
# At the beginning of the script
set -eo pipefail

# Before running tests and benchmarks for each fork
for fork in "$@"; do
  set +e # we don't want prepare.sh, test.sh or hyperfine failing on 1 fork to exit the script early

  # Run prepare script (simplified)
  print_and_execute source "./prepare_$fork.sh"

  # Run the test suite (simplified)
  print_and_execute $TIMEOUT ./test.sh $fork

  # ... (other fork-specific operations)
done
set -e  # Re-enable exit on error after the fork-specific operations
```

## プラットフォーム固有の適応

```
if [ "$(uname -s)" == "Linux" ]; then
  TIMEOUT="timeout -v $RUN_TIME_LIMIT"
else # Assume MacOS
  if [ -x "$(command -v gtimeout)" ]; then
    TIMEOUT="gtimeout -v $RUN_TIME_LIMIT"
  else
    echo -e "${BOLD_YELLOW}WARNING${RESET} gtimeout not available, install with `brew install coreutils` or benchmark runs may take indefinitely long."
  fi
fi
```

## 複数回の実行のタイムスタンプ付きファイル出力

```
filetimestamp=$(date +"%Y%m%d%H%M%S")

# ... (in the loop for each fork)
HYPERFINE_OPTS="--warmup 0 --runs $RUNS --export-json $fork-$filetimestamp-timing.json --output ./$fork-$filetimestamp.out"

# ... (after the benchmarks)
echo "Raw results saved to file(s):"
for fork in "$@"; do
  if [ -f "$fork-$filetimestamp-timing.json" ]; then
      cat $fork-$filetimestamp-timing.json >> $fork-$filetimestamp.out
      rm $fork-$filetimestamp-timing.json
  fi

  if [ -f "$fork-$filetimestamp.out" ]; then
    echo "  $fork-$filetimestamp.out"
  fi
done
```
