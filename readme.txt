Running backup with cron

1. Insatll python-pip
	apt-get install python-pip


2. Install awscli via pip
	pip install awscli
	ascording to aws documentation http://docs.aws.amazon.com/cli/latest/userguide/tutorial-ec2-ubuntu.html
	if something goes wrong try step 3.


3. sudo apt-get install awscli


4. Download the docker image
	docker pull jacobtomlinson/influxdb-to-s3


5. Then run the below command to backup your data for every minute
	sudo docker run -e DATABASE=<database name> -e DATABASE_HOST=172.17.0.1 -e S3_BUCKET=<bucket-name> -e AWS_ACCESS_KEY_ID=<aws-access-key> -e AWS_SECRET_ACCESS_KEY=<aws-secret-access-key> -e AWS_DEFAULT_REGION=us-west-2  035e6e566656 cron "* * * * *"


6. Before backup stop the influxdb server. Then run the below command to restore the latest backup from s3
	sudo docker run -v /var/lib/influxdb:/var/lib/influxdb  -e DATABASE=<database name> -e S3_BUCKET=<bucket-name> -e AWS_ACCESS_KEY_ID=<aws-access-key> -e AWS_SECRET_ACCESS_KEY=<aws-secret-access-key> -e AWS_DEFAULT_REGION=us-west-2 035e6e566656 restore


7. Start your server and checkout the database.



******************************EXAMPLE*******************************

1.Suppose I have a database names backup001 And it has a table meas001 with below data

> select * from meas001
name: meas001
time                host    region  value
----                ----    ------  -----
1496295589874733804 serverA us_west 1
1496295592576762962 serverA us_west 2
1496295593910114739 serverA us_west 3
1496295595485306594 serverA us_west 4
1496295597036830016 serverA us_west 5
1496295598454871103 serverA us_west 6
1496295599917028657 serverA us_west 7
1496295601403602271 serverA us_west 8
1496295604131858753 serverA us_west 9
1496295605912661118 serverA us_west 0


2. Then we took the backup and deleted the data from measurement meas001

> Delete from meas001 where host='serverA'
> select * from meas001 (this command will give an empty output)

3.Now insert the new data in the table

> INSERT meas001,host=serverA,region=us_west value=10
> INSERT meas001,host=serverA,region=us_west value=11
> INSERT meas001,host=serverA,region=us_west value=12
> INSERT meas001,host=serverA,region=us_west value=13
> INSERT meas001,host=serverA,region=us_west value=14
> INSERT meas001,host=serverA,region=us_west value=15
> INSERT meas001,host=serverA,region=us_west value=16
> INSERT meas001,host=serverA,region=us_west value=17
> INSERT meas001,host=serverA,region=us_west value=18
> INSERT meas001,host=serverA,region=us_west value=19
> select * from meas001
name: meas001
time                host    region  value
----                ----    ------  -----
1496295935849517878 serverA us_west 10
1496295937073049587 serverA us_west 11
1496295938550855420 serverA us_west 12
1496295940509373192 serverA us_west 13
1496295941671906125 serverA us_west 14
1496295942904453486 serverA us_west 15
1496295944184424366 serverA us_west 16
1496295946071643501 serverA us_west 17
1496295947575688659 serverA us_west 18
1496295948975874376 serverA us_west 19


4. Now stop the influxdb service and run the restore command for the backup001 database. Now start the influxdb service and chekout your meas001 measurement, you will get the below output

> use backup001
Using database backup001
> select * from meas001
name: meas001
time                host    region  value
----                ----    ------  -----
1496295589874733804 serverA us_west 1
1496295592576762962 serverA us_west 2
1496295593910114739 serverA us_west 3
1496295595485306594 serverA us_west 4
1496295597036830016 serverA us_west 5
1496295598454871103 serverA us_west 6
1496295599917028657 serverA us_west 7
1496295601403602271 serverA us_west 8
1496295604131858753 serverA us_west 9
1496295605912661118 serverA us_west 0
1496295935849517878 serverA us_west 10
1496295937073049587 serverA us_west 11
1496295938550855420 serverA us_west 12
1496295940509373192 serverA us_west 13
1496295941671906125 serverA us_west 14
1496295942904453486 serverA us_west 15
1496295944184424366 serverA us_west 16
1496295946071643501 serverA us_west 17
1496295947575688659 serverA us_west 18
1496295948975874376 serverA us_west 19

****************************EXAMPLE END*****************************


*. To print all docker images
	docker images

*. To print all running docker images
	docker ps

*. To start a docker image
	docker run

*. To get inside a docker image
	docker exec -i -t <containerId> bash
	
*. To kill a docker image
	docker kill <containerId>
