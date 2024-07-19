
- First Identify how works the encryption : 
```c
content[i] = rand1 ^ content[i];
content[i] = ((unsigned char)content[i] << rand2) | ((unsigned char)content[i] >> (8 - rand2));
```

- Another interesting part the rand seed is write in the fourth bytes :
```c
  local_18 = fopen("flag.enc","wb");
  fwrite(&local_40,1,4,local_18);
```

- So we can use the same rand when the flag was encrypted : 
```c
fseek(file, 0, SEEK_END);// Set a pointer a the end of the file 
local_28 = ftell(file);// Grab the file size thanks to the pointer
fseek(file, 0, SEEK_SET);//Set back the pointer to his initial place
local_20 = malloc(local_28);

fread(local_20, 1, local_28, file);

memcpy(&seed, local_20, 4);//Copy the firs four bytes
```

And Now the full code to decode the flag : 

```c
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

void decrypt(int time, char *content, int content_length) {
	srand(time);
	for (int i = 4; i < content_length; i++) {
		int rand1 = rand();
		int rand2 = rand() & 7;
		content[i] = ((unsigned char)content[i] >> rand2) | ((unsigned                   char)content[i] << (8 - rand2));
		content[i] = rand1 ^ content[i];
		}
		for (int i = 4; i < content_length; i++) {
			printf("%c",content[i]);
		}

}

int main() {

FILE *file;
void *local_20;
long int local_28;
int seed;

file = fopen("flag.enc", "rb");

fseek(file, 0, SEEK_END);
local_28 = ftell(file);
fseek(file, 0, SEEK_SET);
local_20 = malloc(local_28);

fread(local_20, 1, local_28, file);
memcpy(&seed, local_20, 4);

printf("Seed: %d\n", seed);
printf("--------------------------------\n");

decrypt(seed, (char *)local_20, local_28);

fclose(file);
free(local_20);
return 0;
}
```
