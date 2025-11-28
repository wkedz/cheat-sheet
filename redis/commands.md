set KEY_NAME VALUE- `set key_name value` - ustawia klucz z podana wartoscia,
get KEY_NAME - `get key_name` - zwraca wartosc klucza,
del KEY_NAME - `del key_name` - usuwa wskazany klucz,
exists KEY_NAME - `exists key_name` - sprawda, czy dany klucz isnieje, zwraca 0/1,
keys * - `keys *` - zwraca klucze o podanym regex,
expire KEY_NAME VALUE - `expire key_name 10` - sprawia, ze dany klucze przestanie istniec po okreslonym czasie,
setex KEY_NAME TIME KEY_VALUE `setex dog 10 Bunia` - sprawia, ze dany klucze przestanie istniec po okreslonym czasie,

