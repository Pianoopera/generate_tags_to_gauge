# generate_tags_to_gauge

正規表現を利用してgaugeで実行するためのtagを生成する

## Composite Action

```yaml

# branch case
- name: Run custom action
  id: run-action
  uses: Pianoopera/generate_tags_to_gauge@main
  with:
    # featureを含んだブランチ名を指定
    github_branch: feature/branch

- name: Outputs
  # outputs.tagsに値を格納
  run: ${{ steps.run-action.outputs.tags }} # outputs is branch
```

```yaml
# fetch case
- name: fetch the tags
  run: |
    git fetch --tags
    echo "FETCHED_TAGS<<EOF" >> $GITHUB_ENV
    echo "$(git tag -l)" >> $GITHUB_ENV
    # list1
    # list2
    # list3
    echo "EOF" >> $GITHUB_ENV

- name: Run custom action
  id: run-action
  uses: Pianoopera/generate_tags_to_gauge@main
  with:
    # github tag --listを受け付ける
    github_tag: ${{ env.FETCHED_TAGS }}

- name: Outputs
  run: ${{ steps.run-action.outputs.tags }} # output is list1|list2|list3

```

## Develop

[act](https://github.com/nektos/act)でローカル環境でもActionsを実行可能

```shell
# ファイル指定
act pull_request -W .github/workflows/test_branch.yml

# git コマンドを利用する場合
act -P ubuntu-latest=catthehacker/ubuntu:act-latest pull_request -W .github/workflows/test_fetch_tag.yml
```
