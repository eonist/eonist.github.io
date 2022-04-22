Notes on enabling https in github-pages and namecheap <!--more-->

### Workflow:
1. Add A records at namecheap that points to github pages ip adresses 185.199.109.153, 185.199.110.153, 185.199.111.153
2. Add cname record that points to the github-pages url xxx.github.io
3. Add cname file to githubpages root domain.xx
4. wait in github settings for the repo. Provisioning of ssl certificates takes 15 min
5. click enforce https
