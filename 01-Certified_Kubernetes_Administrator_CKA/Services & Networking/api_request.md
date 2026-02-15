# Send API Request 

k create token secret-reader -n project-swan

curl $APISERVER/api/v1/secrets --header "Authorization: Bearer $TOKEN" --insecure