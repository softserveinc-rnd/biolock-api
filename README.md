# Biolock API

Biolock is a smart system developed by SoftServe for continuous authentication and monitoring of human well-being, based on electrocardiogram (ECG) analysis with biosensors embedded into a vehicle’s steering wheel and a mobile application

Biolock API is cloud based REST API interface for enrollment and verification of user by ECG data.

![Workflow](img/WorkFlow.png)


Available API:

* [Register](#register)
* [Enroll](#enroll)
* [Verify](#verify)
    


## Register

| Name          | Type          | Description  |
| ------------- |:-------------:| -----|
| name          | String        | Name which will be used for future contact |
| email         | String        | Email which will be used for future contact |



```sh
$ curl -X POST http://34.198.199.171:1111/register  -d 'name=test&email=test@test.com'
```
  Sample response
```json
{"Status":"Ok","apikey":"c6qp3p4i6uulf39ms9f5u1ps6v"}
```

## Enroll

Enroll endpoint can be used to teach the system to recognize specific user on his/her ECG data. The ECG sample with 1000+ heartbeats is recommended to get better accuracy (500 beats is minimum). ECG data should be put in CSV file, where each line represent value in millivolts. Sampling rate should be provided as a parameter to the call. The Enroll action can take from 30 seconds to several minutes to complete depending on server load and data size (on 500 beats it typically takes few seconds).


| Name | Type          | Description  |
| :-------------|:-------------:|:-----|
| apikey| String        | Your valid api key. |
| upload        | String        | CSV file with your ECG data for user which should be authenticated |
| sampling_rate        | Integer        | Sampling rate (samples per second) in CSV file |

Sample request

```sh
curl -X POST --form upload=@/home/user/ecg/user.csv http://34.198.199.171:1111/enroll?apikey=c6qp3p4i6uulf39ms9f5u1ps6v&sampling_rate=512
```
sample response

```json
{"Status":"Enrolled","Accuracy":0.999854,"Message":"Model trained"}
```

## Verify 

Verify endpoint allows testing user ECG (similar format as for Enroll endpoint), as authentication modality. As a result, validation result (pass/fail) and matching probability is returned.


| Name | Type          | Description  |
| :-------------|:-------------:|:-----|
| apikey| String        | Your valid api key. |
| upload        | String        | CSV file with your ECG data for user which should be authenticated |
| sampling_rate        | Integer        | Sampling rate (samples per second) in CSV file |

```sh
curl -X POST --form upload=@/home/user/ecg/testuser.csv http://34.198.199.171:1111/verify?apikey=c6qp3p4i6uulf39ms9f5u1ps6v&sampling_rate=512
```
sample response

```json
{"Status":"Verified","Message":"fail","Probability":4.0381813E-15}  //user didn`t match
or
{"Status":"Verified","UserName":"test","Message":"pass","Probability":0.989748}  //user match
```