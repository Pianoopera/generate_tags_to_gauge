# generate_tags_from_github

GitHubのブランチ名やタグからGaugeで実行するためのタグを生成するComposite Action

## Features

- ブランチ名から`feature/`プレフィックスを除去したタグを生成 (v0.1.0)
- 複数のタグをパイプ区切りで結合 (v0.1.0)
- リポジトリから直接タグを取得して結合 (v0.2.0)

## Usage

### v0.1.0: ブランチ名からタグを生成

```yaml
- name: Run custom action
  id: run-action
  uses: Pianoopera/generate_tags_from_github@main
  with:
    version: "v0.1.0"
    github_branch: feature/branch

- name: Outputs
  run: echo "${{ steps.run-action.outputs.tags }}"
  # Output: branch
```

### v0.1.0: タグリストからパイプ区切りのタグを生成

```yaml
- name: Fetch the tags
  run: |
    git fetch --tags
    echo "FETCHED_TAGS<<EOF" >> $GITHUB_ENV
    git tag -l >> $GITHUB_ENV
    echo "EOF" >> $GITHUB_ENV

- name: Run custom action
  id: run-action
  uses: Pianoopera/generate_tags_from_github@main
  with:
    version: "v0.1.0"
    github_tag: ${{ env.FETCHED_TAGS }}

- name: Outputs
  run: echo "${{ steps.run-action.outputs.tags }}"
  # Output: list1|list2|list3
```

### v0.2.0: リポジトリから直接タグを取得

```yaml
- name: Checkout repository
  uses: actions/checkout@v4

- name: Run custom action
  id: run-action
  uses: Pianoopera/generate_tags_from_github@main
  with:
    version: "v0.2.0"

- name: Outputs
  run: echo "${{ steps.run-action.outputs.newtags }}"
  # Output: v0.1.0|v0.2.0|v0.3.0
```

## Inputs

| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `github_branch` | ブランチ名 (`feature/`プレフィックスを含む) | No | - |
| `github_tag` | タグのリスト (改行区切り) | No | - |
| `version` | アクションのバージョン | No | `v0.1.0` |

## Outputs

| Name | Description | Version |
|------|-------------|---------|
| `tags` | 生成されたタグ (パイプ区切り) | v0.1.0 |
| `newtags` | リポジトリから取得したタグ (パイプ区切り) | v0.2.0 |

## Development

[act](https://github.com/nektos/act)を使用してローカル環境でもActionsを実行可能

```shell
# ブランチテスト
act pull_request -W .github/workflows/test_branch.yml

# タグリストテスト
act pull_request -W .github/workflows/test_list.yml

# タグ取得テスト (gitコマンドを利用)
act -P ubuntu-latest=catthehacker/ubuntu:act-latest pull_request -W .github/workflows/test_fetch_tag.yml
```

## License

MIT License - see [LICENSE](LICENSE) for details
