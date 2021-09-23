# .github

```
given branches.json = [{
    "name":"main",
    "directory: "",
    "is_feature_branch": false
}, {
    "name":"feature/test",
    "directory": "/x-feature-test",
    "is_feature_branch": true
}]

var src_features = read_file_as_array(./branches.json)
    .filter(f => f.is_feature_branch)
    .map(f => "$BUCKET" + f.directory);

var dst_features = az storage blob directory list -c "$STORAGE_CONTAINER"  -d "blog" --account-name "$STORAGE_ACCOUNT" --delimiter '/' --query '[].name | [?ends_with(@, `/`)] | [?starts_with(@, `$BUCKET/x-`)]' --output json

dst_features
    .filter(f => src_features.includes(f) == false)
    .foreach(f => az storage blob directory delete -c "$STORAGE_CONTAINER" -d f --account-name "$STORAGE_ACCOUNT");

```