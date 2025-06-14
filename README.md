
AWSTemplateFormatVersion: 2010-09-09
Resources:
  OrdersTable:
    Type: AWS::DynamoDB::Table
    Properties:
      TableName: AuthorsTable_prod
      AttributeDefinitions:
        - AttributeName: "AuthorName"
          AttributeType: "S"
        - AttributeName: "BookTitle"
          AttributeType: "S"
      KeySchema:
        - AttributeName: "AuthorName"
          KeyType: "HASH"
        - AttributeName: "BookTitle"
          KeyType: "RANGE"
      TimeToLiveSpecification:
        AttributeName: "ExpirationTime"
        Enabled: true
      ProvisionedThroughput:
        ReadCapacityUnits: "10"
        WriteCapacityUnits: "5"
    DependsOn:
      - DynamoDBQueryPolicy

  DynamoDBQueryPolicy:
    Type: "AWS::IAM::Policy"
    Properties:
      PolicyName: DynamoDBQueryPolicy
      PolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: "Allow"
            Action: "dynamodb:Query"
            Resource: "*"
      Roles:
        - Ref: "OrdersTableQueryRole"

  OrdersTableQueryRole:
    Type: "AWS::IAM::Role"
    Properties:
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: "Allow"
            Principal:
              Service:
                - "dynamodb.amazonaws.com"
            Action:
              - "sts:AssumeRole"
      Path: "/"
