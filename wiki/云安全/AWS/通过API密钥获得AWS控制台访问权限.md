	> git clone https://github.com/NetSPI/aws_consoler
	> aws_consoler -v -a AKIA[REDACTED] -s [REDACTED]
	2020-03-13 19:44:57,800 [aws_consoler.cli] INFO: Validating arguments...
	2020-03-13 19:44:57,801 [aws_consoler.cli] INFO: Calling logic.
	2020-03-13 19:44:57,820 [aws_consoler.logic] INFO: Boto3 session established.
	2020-03-13 19:44:58,193 [aws_consoler.logic] WARNING: Creds still permanent, creating federated session.
	2020-03-13 19:44:58,698 [aws_consoler.logic] INFO: New federated session established.
	2020-03-13 19:44:59,153 [aws_consoler.logic] INFO: Session valid, attempting to federate as arn:aws:sts::123456789012:federated-user/aws_consoler.
	2020-03-13 19:44:59,668 [aws_consoler.logic] INFO: URL generated!
	https://signin.aws.amazon.com/federation?Action=login&Issuer=consoler.local&Destination=https%3A%2F%2Fconsole.aws.amazon.com%2Fconsole%2Fhome%3Fregion%3Dus-east-1&SigninToken=[REDACTED